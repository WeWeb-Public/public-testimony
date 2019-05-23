<template>
    <div class="testimony">
        <!-- wwManager:start -->
        <wwSectionEditMenu v-bind:sectionCtrl="sectionCtrl"></wwSectionEditMenu>
        <!-- wwManager:end -->
        <!-- BLOCK MANAGER -->
        <div class="block-manager">
            <!-- wwManager:start -->
            <span v-show="editMode" v-if="section.data.leftBlocks.length > 1" class="remove-block-btn" @click="deleteBlock()">Remove Block</span>
            <span v-show="editMode" class="add-block-btn" @click="addBlock()">Add Block</span>
            <!-- wwManager:end -->
            <span v-show="section.data.leftBlocks.length > 1">{{this.indexBlock+1}} sur {{this.section.data.leftBlocks.length}}</span>
            <span v-show="section.data.leftBlocks.length > 1" class="next-block-btn" @click="nextBlock()">
                <wwObject v-bind:ww-object="section.data.nextBlock"></wwObject>
            </span>
        </div>
        <div class="news-container">
            <div class="left-container">
                <div v-for="(block, index) in section.data.leftBlocks" :key="block.uniqueId">
                    <div class="block" :class="getPos(section.data.leftBlocks, index)">
                        <!-- BACKGROUND -->
                        <wwObject class="block-image" v-bind:ww-object="block.bg" ww-default-object-type="ww-image" style="height: 100%" ww-category="background"></wwObject>
                        <!-- WWOBJS -->
                        <div class="block-blocks">
                            <wwLayoutColumn tag="div" ww-default="ww-columns" :ww-list="block.blocks" @ww-add="add(block.blocks, $event)" @ww-remove="remove(block.blocks, $event)">
                                <div v-for="wwObj in block.blocks" :key="wwObj.uniqueId">
                                    <wwObject v-bind:ww-object="wwObj"></wwObject>
                                </div>
                            </wwLayoutColumn>
                        </div>
                    </div>
                </div>
            </div>
            <div class="right-container">
                <div v-for="(block, index) in section.data.rightBlocks" :key="block.uniqueId">
                    <div class="block" :class="getPos(section.data.rightBlocks, index)">
                        <!-- BACKGROUND -->
                        <wwObject class="block-image" v-bind:ww-object="block.bg" ww-default-object-type="ww-image" style="height: 100%" ww-category="background"></wwObject>
                        <!-- WWOBJS -->
                        <wwLayoutColumn class="block-blocks" tag="div" ww-default="ww-columns" :ww-list="block.blocks" @ww-add="add(block.blocks, $event)" @ww-remove="remove(block.blocks, $event)">
                            <div v-for="wwObj in block.blocks" :key="wwObj.uniqueId">
                                <wwObject v-bind:ww-object="wwObj"></wwObject>
                            </div>
                        </wwLayoutColumn>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "__COMPONENT_NAME__",
    props: {
        sectionCtrl: Object
    },
    data() {
        return {
            indexBlock: 0
        }
    },
    computed: {
        section() {
            return this.sectionCtrl.get();
        },
        // wwManager:start
        editMode() {
            return this.sectionCtrl.getEditMode() == 'CONTENT'
        }
        // wwManager:end
    },
    created() {
        //Initialize section data
        this.section.data = this.section.data || {};
        this.section.data.leftBlocks = this.section.data.leftBlocks || [this.getNewLeftBlock()];
        this.section.data.rightBlocks = this.section.data.rightBlocks || [this.getNewRightBlock()];

        if (!this.section.data.nextBlock) {
            this.section.data.nextBlock = wwLib.wwObject.getDefault({ type: 'ww-icon', data: { icon: 'wwi wwi-plus' } })
        }

        this.sectionCtrl.update(this.section)
    },
    methods: {
        // wwManager:start
        add(array, options) {
            array.splice(options.index, 0, options.wwObject);
            this.sectionCtrl.update(this.section);
        },
        remove(array, options) {
            array.splice(options.index, 1);
            this.sectionCtrl.update(this.section);
        },

        addBlock() {
            this.section.data.leftBlocks.push(this.getNewLeftBlock())
            this.section.data.rightBlocks.push(this.getNewRightBlock())
            this.indexBlock = this.section.data.leftBlocks.length - 1
            this.sectionCtrl.update(this.section)
        },
        deleteBlock() {
            this.section.data.leftBlocks.splice(this.indexBlock, 1)
            this.section.data.rightBlocks.splice(this.indexBlock, 1)
            this.indexBlock = 0
            this.sectionCtrl.update(this.section)
        },
        // wwManager:end
        getNewRightBlock() {
            return {
                uniqueId: wwLib.wwUtils.getUniqueId(),
                bg: wwLib.wwObject.getDefault({ type: 'ww-color' }),
                blocks: []
            }
        },
        getNewLeftBlock() {
            return {
                uniqueId: wwLib.wwUtils.getUniqueId(),
                bg: wwLib.wwObject.getDefault({ type: 'ww-image' }),
                blocks: []
            }
        },
        nextBlock() {
            this.indexBlock = (this.indexBlock + 1) % this.section.data.leftBlocks.length
        },
        getPos(array, idx) {
            if (this.indexBlock === idx)
                return 'active'
            if (idx + 1 === this.indexBlock || (idx === (array.length - 1) && this.indexBlock === 0))
                return 'previous'
            // if (this.indexBlock + 1 === idx || (this.indexBlock === (array.length-1) && idx === 0))
            //     return 'next'
            return ''
        }
    }
};
</script>

<style lang="scss" scoped>
.testimony {
    position: relative;
    overflow: hidden;

    .block-manager {
        position: absolute;
        bottom: 1%;
        right: 1%;
        display: flex;
        align-items: center;
        pointer-events: all;
        margin-right: 10%;
        z-index: 10;

        @media (max-width: 1024px) {
            top: 51%;
            bottom: unset;
        }

        .next-block-btn {
            cursor: pointer;
            position: relative;
            display: inline-block;
            margin: 0 5px;
        }
        .add-block-btn {
            position: relative;
            cursor: pointer;
            background: #19947c;
            color: white;
            font-weight: bold;
            border-radius: 20px;
            padding: 4px 15px;
            margin: 0 5px;
        }
        .remove-block-btn {
            position: relative;
            cursor: pointer;
            background: #ce003b;
            color: white;
            font-weight: bold;
            border-radius: 20px;
            padding: 4px 15px;
            margin: 0 5px;
        }
    }

    .news-container {
        position: relative;
        display: flex;
        flex-wrap: wrap;
        height: 75vh;
        width: 100%;
        // width: 60%;
        // margin-left: 20%;

        @media (min-width: 992px) {
            width: 80%;
            margin-left: 10%;
        }

        .block {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: none;
            background: white;
            pointer-events: all;

            .block-image {
                position: absolute;
                top: 0;
                left: 0;
                width: 100%;
                height: 100%;
            }
            .block-blocks {
            }
            &.active {
                display: unset;
                z-index: 2;
            }
            &.previous {
                display: unset;
                z-index: 1;
            }
            &.next {
            }
        }

        .left-container {
            position: relative;
            width: 60%;
            margin-left: 20%;
            overflow: hidden;

            @media (min-width: 992px) {
                width: 33.3333%;
                margin-left: 0%;
            }

            .block {
                overflow: hidden;
            }

            .block.active {
                animation: 1s block-anim-enter ease-in;
                animation-fill-mode: forwards;
            }
            .block.previous {
                animation: 1s block-anim-leave ease-in;
                animation-fill-mode: forwards;
                animation-delay: 0.1s;
            }
        }
        .right-container {
            position: relative;
            width: 100%;

            @media (min-width: 992px) {
                width: 66.6666%;
            }

            .block {
                max-height: calc(100% - 40px);
                overflow-y: auto;
                overflow-x: hidden;
                margin-top: 40px;

                @media (min-width: 992px) {
                    margin-top: 0;
                    height: auto;
                }
            }

            .block.active {
                opacity: 0;
                animation: 1s content-fade-enter 1s ease;
                animation-fill-mode: forwards;
            }
            .block.previous {
                opacity: 1;
                animation: 1s content-fade-leave ease-out;
                animation-fill-mode: forwards;
            }
        }
    }
}

@keyframes block-anim-leave {
    0% {
    }
    100% {
        transform: translateY(-100%);
    }
}

@keyframes block-anim-enter {
    0% {
        transform: translateY(100%);
    }
    100% {
    }
}

@keyframes content-fade-enter {
    0% {
        opacity: 0;
    }
    100% {
        opacity: 1;
    }
}

@keyframes content-fade-leave {
    0% {
        opacity: 1;
    }
    100% {
        opacity: 0;
    }
}

@media (max-width: 1024px) {
    @keyframes block-anim-leave {
        0% {
        }
        100% {
            transform: translateX(100%);
        }
    }

    @keyframes block-anim-enter {
        0% {
            transform: translateX(-100%);
        }
        100% {
        }
    }
}
</style>
