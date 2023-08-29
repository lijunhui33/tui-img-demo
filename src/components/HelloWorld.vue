<template>
    <div style="display: flex">
        <div style="width: 300px">
            <!-- <button @click="set">切换图片</button> -->
            <div>
                点击下方的在线图片切换画布要编辑的图片，或者点击画布右边的按钮上传本地图片
            </div>
            <img
                :src="img1"
                alt=""
                srcset=""
                style="width: 300px"
                @click="set(img1)"
            />
            <img
                :src="img2"
                alt=""
                srcset=""
                style="width: 300px"
                @click="set(img2)"
            />
            <button @click="download">导出图片</button>
        </div>
        <div style="height: 800px; width: 100%">
            <div id="edit-box" style="width: 100%; height: 100%">
                <div id="tui-image-editor"></div>
            </div>
        </div>
    </div>
</template>
<script>
import "tui-image-editor/dist/tui-image-editor.css";
import "tui-color-picker/dist/tui-color-picker.css";
const ImageEditor = require("tui-image-editor");
const locale_zh = {
    // override default English locale to your custom
    Crop: "裁剪",
    Resize: "调整大小",
    DeleteAll: "全部删除",
    ZoomIn: "放大",
    ZoomOut: "缩小",
    Delete: "删除",
    Undo: "撤销",
    Redo: "反撤销",
    Reset: "重置",
    Flip: "镜像",
    Rotate: "旋转",
    Draw: "画",
    Shape: "形状标注",
    Icon: "图标标注",
    Text: "文字标注",
    // Mask: "遮罩",
    Filter: "滤镜",
    Bold: "加粗",
    Italic: "斜体",
    Underline: "下划线",
    Left: "左对齐",
    Center: "居中",
    Right: "右对齐",
    Color: "颜色",
    "Text size": "字体大小",
    Custom: "自定义",
    Square: "正方形",
    Apply: "应用",
    Cancel: "取消",
    "Flip X": "X 轴",
    "Flip Y": "Y 轴",
    Range: "区间",
    Stroke: "描边",
    Fill: "填充",
    Circle: "圆",
    Triangle: "三角",
    Rectangle: "矩形",
    Free: "曲线",
    Straight: "直线",
    Arrow: "箭头",
    "Arrow-2": "箭头2",
    "Arrow-3": "箭头3",
    "Star-1": "星星1",
    "Star-2": "星星2",
    Polygon: "多边形",
    Location: "定位",
    Heart: "心形",
    Bubble: "气泡",
    "Custom icon": "自定义图标",
    "Load Mask Image": "加载蒙层图片",
    Grayscale: "灰度",
    Blur: "模糊",
    Sharpen: "锐化",
    Emboss: "浮雕",
    "Remove White": "除去白色",
    Distance: "距离",
    Brightness: "亮度",
    Noise: "噪音",
    "Color Filter": "彩色滤镜",
    Sepia: "棕色",
    Sepia2: "棕色2",
    Invert: "负片",
    Pixelate: "像素化",
    Threshold: "阈值",
    Tint: "色调",
    Multiply: "正片叠底",
    Blend: "混合色",
    Hand: "拖拽",
    History: "历史",
    Load: "本地图片上传",
    Width: "宽度",
    Height: "高度",
    "Lock Aspect Ratio": "等比例",
    // etc...
};
const customTheme = {
    // download button
    "downloadButton.backgroundColor": "#fdba3b",
    "downloadButton.border": "1px solid #fdba3b",
    "downloadButton.color": "#fff",
    "downloadButton.fontFamily": "NotoSans, sans-serif",
    "downloadButton.fontSize": "12px",
    "downloadButton.display": "none", // 可以直接隐藏掉
    // image 坐上角度图片
    "common.bi.image": "https://uploadbeta.com/api/pictures/random/", // 在这里换上你喜欢的logo图片
    "common.bisize.width": "0px",
    "common.bisize.height": "0px",
    "common.backgroundImage": "none",
    "common.border": "1px solid #444",

    "Mask.display": "none",
};
const img1 =
    "https://gimg3.baidu.com/search/src=http%3A%2F%2Fpics1.baidu.com%2Ffeed%2F83025aafa40f4bfb34053bdc0866f0fcf6361812.jpeg%40f_auto%3Ftoken%3Dee2d57666cd27c587703661edb6ffc85&refer=http%3A%2F%2Fwww.baidu.com&app=2021&size=f360,240&n=0&g=0n&q=75&fmt=auto?sec=1691514000&t=38ac49f490517a164749a5ed7eecfbc3";
const img2 =
    "https://img1.baidu.com/it/u=3888243655,1390725140&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=333";
function downloadBase64Img(base64URL, fileName) {
    // 创建a标签，用于触发下载
    const a = document.createElement("a");
    // 将 a 标签的 download 属性设置为要下载的文件名
    a.download = fileName || "image";
    // 创建 Blob 对象，并获取 base64 数据的 MIME 类型
    const mimeType = base64URL.match(/:(.*?);/)[1];
    // 将 base64 数据转换为字节数组
    const byteCharacters = atob(base64URL.split(",")[1]);
    const byteNumbers = new Array(byteCharacters.length);
    // 将字节数组填充到 Uint8Array 中
    for (let i = 0; i < byteCharacters.length; i++) {
        byteNumbers[i] = byteCharacters.charCodeAt(i);
    }
    const byteArray = new Uint8Array(byteNumbers);
    // 创建 Blob 对象
    const blob = new Blob([byteArray], { type: mimeType });
    // 将 Blob 对象的 URL 赋值给 a 标签的 href 属性
    a.href = URL.createObjectURL(blob);
    // 将a标签暂时添加到 body 中，触发下载
    document.body.appendChild(a);
    a.click();
    // 下载完成后，将 a 标签从 body 中移除
    document.body.removeChild(a);
}

export default {
    data() {
        return {
            instance: null,
            img1,
            img2,
        };
    },
    mounted() {
        this.instance = new ImageEditor(
            document.querySelector("#tui-image-editor"),
            {
                includeUI: {
                    loadImage: {
                        path: img1,
                        name: "image",
                    },
                    initMenu: "draw",
                    menuBarPosition: "bottom",
                    locale: locale_zh, // 本地化语言为中文
                    theme: customTheme,
                    menu: [
                        "resize",
                        "crop",
                        "rotate",
                        "draw",
                        "shape",
                        "icon",
                        "text",
                        "filter",
                        "flip",
                    ], // 底部菜单按钮列表 隐藏镜像flip和遮罩mask
                },
            }
        );
    },
    methods: {
        download() {
            console.log(this.instance.toDataURL());
            downloadBase64Img(this.instance.toDataURL(), "示例图片");
        },
        set(src) {
            this.instance = null;
            const innerHtml = document.querySelector("#edit-box");
            innerHtml.innerHTML = "";
            const newDom = document.createElement("div");
            newDom.id = "tui-image-editor";
            innerHtml.append(newDom);
            this.$nextTick(() => {
                this.instance = new ImageEditor(
                    document.querySelector("#tui-image-editor"),
                    {
                        includeUI: {
                            loadImage: {
                                path: src,
                                name: "image",
                            },
                            initMenu: "draw",
                            menuBarPosition: "bottom",
                            locale: locale_zh, // 本地化语言为中文
                            theme: customTheme,
                            menu: [
                                "resize",
                                "crop",
                                "rotate",
                                "draw",
                                "shape",
                                "icon",
                                "text",
                                "filter",
                                "flip",
                            ], // 底部菜单按钮列表 隐藏镜像flip和遮罩mask
                        },
                    }
                );
            });
        },
    },
};
</script>
