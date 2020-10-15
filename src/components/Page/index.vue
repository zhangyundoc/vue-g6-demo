<template>
    <div class="page">
        <div
            :id="pageId"
            class="graph-container"
            style="position: relative"
        ></div>
    </div>
</template>


<script>
    import G6 from "@antv/g6";
    import { initBehavors } from "@/behavior";
    import ContextMenu from "../ContextMenu";
    export default {
        data() {
            return {
                pageId: "graph-container",
                graph: null,
            };
        },
        props: {
            height: {
                type: Number,
                default: 0,
            },
            width: {
                type: Number,
                default: 0,
            },
            data: {
                type: Object,
                default: () => {},
            },
        },
        created() {
            initBehavors();
        },
        mounted() {
            this.$nextTick(() => {
                this.init();
            });
        },
        methods: {
            init() {
                const height = this.height - 42;
                const width = this.width - 400;

                const contextMenu = new G6.Menu({
                    getContent(graph) {
                        console.log("graph", graph);
                        return `<ul>
                <li title='1'>测试01</li>
                <li title='2'>测试02</li>
                <li>测试03</li>
                <li>测试04</li>
                <li>测试05</li>
              </ul>`;
                    },
                    handleMenuClick: (target, item) => {
                        console.log(target, item);
                    },
                    // offsetX and offsetY include the padding of the parent container
                    // 需要加上父级容器的 padding-left 16 与自身偏移量 10
                    offsetX: 16 + 10,
                    // 需要加上父级容器的 padding-top 24 、画布兄弟元素高度、与自身偏移量 10
                    offsetY: 24 + 28 + 10,
                    // the types of items that allow the menu show up
                    // 在哪些类型的元素上响应
                    itemTypes: ["node", "edge"],
                });

                this.graph = new G6.Graph({
                    container: "graph-container",
                    height: height,
                    width: width,
                    plugins: [contextMenu],
                    defaultNode: {
                        type: "rect",
                    },
                    modes: {
                        // 支持的 behavior
                        default: [
                            "drag-canvas",
                            "zoom-canvas",
                            "hover-node",
                            "select-node",
                            "hover-edge",
                            "keyboard",
                            "customer-events",
                            "add-menu",
                        ],
                        mulitSelect: ["mulit-select"],
                        addEdge: ["add-edge"],
                        moveNode: ["drag-item"],
                    },
                });
                const { editor, command } = this.$parent;
                editor.emit("afterAddPage", { graph: this.graph, command });

                this.readData();
            },
            readData() {
                let data = this.data;
                if (data) {
                    this.graph.read(data);
                }
            },
        },
    };
</script>

<style scoped>
    .page {
        margin-left: 200px;
        margin-right: 200px;
    }
</style>
