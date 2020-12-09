<!--
 * @Author: Zhang Yingying
 * @Date: 2020-12-09 23:24:20
 * @LastEditors: Zhang Yingying
 * @LastEditTime: 2020-12-10 00:05:16
-->

# 1、问题起源

由于 Vue 基于组件化的设计，得益于这个思想，我们在 Vue 的项目中可以通过封装组件提高代码的复用性。根据我目前的使用心得，知道 Vue 拆分组件至少有两个优点：
1、代码复用。2、代码拆分
在基于 element-ui 开发的项目中，可能我们要写出一个类似的调度弹窗功能，我们很容易编写出以下代码：

```vue
<template>
    <div>
        <el-dialog :visible.sync="cnMapVisible">我是中国地图的弹窗</el-dialog>
        <el-dialog :visible.sync="usaMapVisible">我是美国地图的弹窗</el-dialog>
        <el-dialog :visible.sync="ukMapVisible">我是英国地图的弹窗</el-dialog>
        <el-button @click="openChina">打开中国地图</el-button>
        <el-button @click="openUSA">打开美国地图</el-button>
        <el-button @click="openUK">打开英国地图</el-button>
    </div>
</template>
<script>
export default {
    name: "View",
    data() {
        return {
            // 对百度地图和谷歌地图的一些业务处理代码 省略
            cnMapVisible: false,
            usaMapVisible: false,
            ukMapVisible: false,
        };
    },
    methods: {
        // 对百度地图和谷歌地图的一些业务处理代码 省略
        openChina() {},
        openUSA() {},
        openUK() {},
    },
};
</script>
```

上述代码存在的问题非常多，首先当我们的弹窗越来越多的时候，我们会发现此时需要定义越来越多的变量去控制这个弹窗的显示或者隐藏。
由于当我们的弹窗的内部还有业务逻辑需要处理，那么此时会有相当多的业务处理代码夹杂在一起（比如我调用中国地图我需要用高德地图或者百度地图，而调用美国、英国地图我只能用谷歌地图，这会使得两套业务逻辑分别位于一个文件，严重加大了业务的耦合度）

我们按照分离业务，降低耦合度的原则，将代码按以下思路进行拆分：
1、View.vue

```vue
<template>
    <div>
        <el-dialog ref="china">我是中国地图的弹窗</el-dialog>
        <el-dialog ref="usa">我是美国地图的弹窗</el-dialog>
        <el-dialog ref="uk">我是英国地图的弹窗</el-dialog>
        <el-button @click="openChina">打开中国地图</el-button>
        <el-button @click="openUSA">打开美国地图</el-button>
        <el-button @click="openUK">打开英国地图</el-button>
    </div>
</template>
<script>
export default {
    name: "View",
    data() {
        return {
            // 对百度地图和谷歌地图的一些业务处理代码 省略
            cnMapVisible: false,
            usaMapVisible: false,
            ukMapVisible: false,
        };
    },
    methods: {
        // 对百度地图和谷歌地图的一些业务处理代码 省略
        openChina() {},
        openUSA() {},
        openUK() {},
    },
};
</script>
```

2、BaiduMapDialog.vue

```vue
<template>
    <div>
        <el-dialog ref="china">我是中国地图的弹窗</el-dialog>
        <el-dialog ref="usa">我是美国地图的弹窗</el-dialog>
        <el-dialog ref="uk">我是英国地图的弹窗</el-dialog>
        <el-button @click="openChina">打开中国地图</el-button>
        <el-button @click="openUSA">打开美国地图</el-button>
        <el-button @click="openUK">打开英国地图</el-button>
    </div>
</template>
<script>
export default {
    name: "BaiduMapDialog",
    data() {
        return {
            // 对百度地图和谷歌地图的一些业务处理代码 省略
            baiduMapVisible: false,
        };
    },
    methods: {
        // 对百度地图和谷歌地图的一些业务处理代码 省略
        openChina() {},
        openUSA() {},
        openUK() {},
    },
};
</script>
```

3、GoogleMapDialog.vue

# 2、问题分析

抽象

# 3、设计

对 header 的处理 （对 slots 的处理）

对 body 的处理 （对 slots 的处理）

对 footer 的处理（对 slots 的处理）

对弹窗事件的支持 重写 或 外部传入

对参数的支持 重写 或 外部传入

```js
import Vue from "vue";
import Component from "vue-class-component";
@Component({
    name: "BaseDialog",
})
export default class BaseDialog extends Vue {
    visible = false;

    t = "";

    loading = false;

    attrs = {};

    get title() {
        return this.t;
    }

    setTitle(title) {
        this.t = title;
    }

    open() {
        console.log("弹窗打开，我啥也不做");
    }

    close() {
        console.log("弹窗关闭，我啥也不做");
    }

    opened() {
        console.log("弹窗打开，我啥也不做");
    }

    closed() {
        console.log("弹窗关闭，我啥也不做");
    }

    showLoading() {
        this.loading = true;
    }

    closeLoading() {
        this.loading = false;
    }

    openDialog() {
        this.visible = true;
    }

    closeDialog() {
        if (this.loading) {
            this.$message.warning("请等待操作完成!");
            return;
        }
        this.visible = false;
    }

    onSubmit() {
        this.closeDialog();
    }

    onClose() {
        this.closeDialog();
    }

    /*
     构建弹窗的Header
     */
    _createHeader(h) {
        var slotHeader = this.$scopedSlots["header"] || this.$slots["header"];
        if (typeof slotHeader === "function") {
            return slotHeader();
        }
        var renderHeader = this.renderHeader;
        if (typeof renderHeader === "function") {
            return <div slot="header">{renderHeader(h)}</div>;
        }
    }

    /**
     * 构建弹窗的Body部分
     */
    _createBody(h) {
        var slotBody = this.$scopedSlots["default"] || this.$slots["default"];
        if (typeof slotBody === "function") {
            return slotBody();
        }
        var renderBody = this.renderBody;
        if (typeof renderBody === "function") {
            return renderBody(h);
        }
    }

    /**
     * 构建弹窗的Footer
     */
    _createFooter(h) {
        var footer = this.$scopedSlots.footer || this.$slots.footer;
        if (typeof footer == "function") {
            return footer();
        }
        var renderFooter = this.renderFooter;
        if (typeof renderFooter === "function") {
            return <div slot="footer">{renderFooter(h)}</div>;
        }

        return this.defaultFooter(h);
    }

    defaultFooter(h) {
        return (
            <div slot="footer">
                <el-button
                    type="primary"
                    loading={this.loading}
                    on-click={() => {
                        this.onSubmit();
                    }}
                >
                    保存
                </el-button>
                <el-button
                    on-click={() => {
                        this.onClose();
                    }}
                >
                    取消
                </el-button>
            </div>
        );
    }

    createContainer(h) {
        var { title, ...rest } = Object.assign({}, this.$attrs, this.attrs);
        return (
            <el-dialog
                {...{
                    props: {
                        ...rest,
                        visible: this.visible,
                        title: this.title || title || "弹窗",
                        beforeClose: this.closeDialog,
                    },
                    attrs: {
                        ...rest,
                    },
                    on: {
                        close: this.close,
                        closed: this.closed,
                        opened: this.opened,
                        open: this.open,
                    },
                }}
            >
                {this._createHeader(h)}
                {this._createBody(h)}
                {this._createFooter(h)}
            </el-dialog>
        );
    }

    render(h) {
        return this.createContainer(h);
    }
}
```

# 4、组件应用

组件调用

## 4、1 不使用 Composition API

```vue

```

## 4、2 使用 Composition API

```vue

```
