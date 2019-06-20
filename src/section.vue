<template>
    <div class="feature_E">
        <!-- wwManager:start -->
        <wwSectionEditMenu :sectionCtrl="sectionCtrl" :options="openOptions"></wwSectionEditMenu>
        <!-- wwManager:end -->
        <div class="feature_E">
            <wwObject class="background" :ww-object="section.data.background" ww-category="background"></wwObject>
            <!--TOP WWOBJS-->
            <div class="top-ww-objs">
                <wwLayoutColumn
                    tag="div"
                    ww-default="ww-image"
                    :ww-list="section.data.topWwObjs"
                    class="top-ww-obj"
                    @ww-add="add(section.data.topWwObjs, $event)"
                    @ww-remove="remove(section.data.topWwObjs, $event)"
                >
                    <wwObject v-for="topWwObj in section.data.topWwObjs" :key="topWwObj.uniqueId" :ww-object="topWwObj"></wwObject>
                </wwLayoutColumn>
            </div>
            <!--CONTENT-->
            <div class="row">
                <div class="features-container container-margin container-size">
                    <div class="features">
                        <!-- wwManager:start -->
                        <wwContextMenu tag="div" class="contextmenu" v-if="editMode" @ww-add="addNewFeature($event)" @ww-remove="removeFeature($event)">
                            <div class="wwi wwi-config"></div>
                        </wwContextMenu>
                        <!-- wwManager:end -->
                        <wwLayoutColumn tag="div" ww-default="ww-text" :ww-list="section.data.features">
                            <div v-for="feature in section.data.features" :key="feature.uniqueId" class="feature">
                                <div
                                    class="feature-title"
                                    :class="{'active': feature.activeTitle}"
                                    :style="{'background-color': section.data.activeButtonBgColorValue}"
                                    @click="setActiveFeature(feature)"
                                >
                                    <wwObject tag="div" :ww-object="feature.title" ww-object-types-allowed="['text']"></wwObject>
                                </div>
                                <div class="xs-content" :class="{'active-mobile': feature.activeTitle}" :style="{'border-color': section.data.activeButtonBorderColor}">
                                    <div v-for="content in activeFeature.contents" :key="content.uniqueId" class="feature-content">
                                        <wwObject tag="div" :ww-object="content"></wwObject>
                                    </div>
                                </div>
                            </div>
                        </wwLayoutColumn>
                    </div>
                    <div class="content">
                        <div class="content-mask">
                            <div class="content-bg-color confirm-border" :style="{'background-color': section.data.activeBorderColorValue}"></div>
                            <div class="content-container">
                                <wwLayoutColumn
                                    tag="div"
                                    ww-default="ww-text"
                                    :ww-list="activeFeature.contents"
                                    class="feature-content"
                                    @ww-add="add(activeFeature.contents, $event)"
                                    @ww-remove="remove(activeFeature.contents, $event)"
                                >
                                    <wwObject v-for="content in activeFeature.contents" :key="content.uniqueId" :ww-object="content"></wwObject>
                                </wwLayoutColumn>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <!--BOTTOM WWOBJS-->
            <div class="bottom-ww-objs">
                <wwLayoutColumn
                    tag="div"
                    ww-default="ww-image"
                    :ww-list="section.data.bottomWwObjs"
                    class="top-ww-obj"
                    @ww-add="add(section.data.bottomWwObjs, $event)"
                    @ww-remove="remove(section.data.bottomWwObjs, $event)"
                >
                    <wwObject v-for="bottomWwObj in section.data.bottomWwObjs" :key="bottomWwObj.uniqueId" :ww-object="bottomWwObj"></wwObject>
                </wwLayoutColumn>
            </div>
        </div>
    </div>
</template>

<!-- This is your Javascript -->
<!-- ✨ Here comes the magic ✨ -->
<script>
export default {
    name: "__COMPONENT_NAME__",
    props: {
        // The section object is passed to you.
        // It contains all the info and data about the section
        // Use it has you like !
        sectionCtrl: Object
    },
    data() {
        return {
            editingSection: false,
            activeFeature: null,
            containerSize: 300
        };
    },
    computed: {
        section() {
            return this.sectionCtrl.get();
        },
        editMode() {
            return this.sectionCtrl.getEditMode() == 'CONTENT'
        },
    },
    created() {
        this.initData();
        //Initialize section data
    },
    mounted() {
        this.init();
    },
    methods: {
        initData() {
            //Init objects
            let needUpdate = false;

            this.section.data.features = this.section.data.features || [
                this.getNewFeature()
            ];


            this.section.data.activeFeature = this.section.data.features[0];
            this.section.data.borderColorValue =
                this.section.data.borderColorValue || "#54AB26";
            this.section.data.activeButtonBgColorValue =
                this.section.data.activeButtonBgColorValue || "#9AD575";


            this.section.data.activeBorderColorValue =
                this.section.data.activeBorderColorValue || "#54AB26";

            this.section.data.defaultButtonBorderColor =
                this.section.data.defaultButtonBorderColor || "#54AB26";

            this.section.data.activeButtonBorderColor =
                this.section.data.activeButtonBorderColor || "#54AB26";

            this.section.data = this.section.data || {};

            if (!this.section.data.background) {
                this.section.data.background = wwLib.wwObject.getDefault({
                    type: "ww-image",
                    data: {
                        url:
                            "https://wewebdev.s3-eu-west-1.amazonaws.com/public/images/urban/ChicagoSkyline1.jpg"
                    }
                });
                needUpdate = true;
            }

            if (!this.section.data.content) {
                this.section.data.content = wwLib.wwObject.getDefault({
                    type: "ww-text"
                });
                needUpdate = true;
            }
            if (!this.section.data.title) {
                this.section.data.title = wwLib.wwObject.getDefault({
                    type: "ww-text"
                });
                needUpdate = true;
            }

            if (!this.section.data.activeFeature) {
                this.section.data.activeFeature = wwLib.wwObject.getDefault({
                    type: "ww-text"
                });
                needUpdate = true;
            }
            if (_.isEmpty(this.section.data.topWwObjs)) {
                this.section.data.topWwObjs = [];
                needUpdate = true;
            }
            if (_.isEmpty(this.section.data.bottomWwObjs)) {
                this.section.data.bottomWwObjs = [];
                needUpdate = true;
            }
            this.setActiveFeature(this.section.data.activeFeature);
            if (needUpdate) {
                this.sectionCtrl.update(this.section);
            }
        },
        init() {
            this.setActiveFeature(this.section.data.activeFeature);
            this.$emit("ctrl-ready", "feature_E");
        },

        borderAnim() {
            let sectionElement = this.$el;

            if (this.$el) {
                setTimeout(function () {
                    sectionElement
                        .querySelector(".content-bg-color")
                        .classList.remove("enable-animation");
                    setTimeout(function () {
                        sectionElement
                            .querySelector(".content-bg-color")
                            .classList.add("enable-animation");
                    }, 50);
                }, 1);
            }
        },
        async openOptions() {
            try {

                wwLib.wwPopups.addStory('WWFEATURE_CUSTOM', {
                    title: {
                        en: 'Fill in code',
                        fr: 'Inserer le code'
                    },
                    type: 'wwPopupForm',
                    storyData: {
                        fields: [
                            {
                                label: {
                                    en: 'Button border color:',
                                    fr: 'Couleur de la bordure :'
                                },
                                type: 'color',
                                key: 'defaultButtonBorderColor',
                                valueData: 'section.data.defaultButtonBorderColor',
                                desc: {
                                    en: 'Choose the default color of the button border color:',
                                    fr: 'Changer la couleur par default de la bordure du bouton :'
                                },
                            },
                            {
                                label: {
                                    en: 'Border color after clic:',
                                    fr: 'Couleur de la bordure après le clic :'
                                },
                                type: 'color',
                                key: 'contentBorderColor',
                                valueData: 'section.data.activeBorderColorValue',
                                desc: {
                                    en: 'Choose the border color of the banner',
                                    fr: 'Couleur de la bordure de la bannière:'
                                },
                            },
                            {
                                label: {
                                    en: 'Active button color:',
                                    fr: 'Couleur du bouton actif :'
                                },
                                type: 'color',
                                key: 'activeButtonBorderColor',
                                valueData: 'section.data.activeButtonBorderColor',
                                desc: {
                                    en: 'Choose the color of the active button border color:',
                                    fr: 'Changer la couleur de la bordure du bouton actif :'
                                },
                            },

                        ]
                    },
                    buttons: {
                        NEXT: {
                            text: {
                                en: 'Finish',
                                fr: 'Terminer'
                            },
                            next: null
                        }
                    }
                })
                let options = {
                    firstPage: 'WWFEATURE_CUSTOM',
                    data: {
                        section: this.section,
                    },
                }

                const result = await wwLib.wwPopups.open(options)
                let needUpdate = false
                if (typeof (result) != 'undefined') {
                    if (typeof (result.defaultButtonBorderColor) != 'undefined') {
                        this.section.data.defaultButtonBorderColor = result.defaultButtonBorderColor
                        needUpdate = true
                    }
                    if (typeof (result.contentBorderColor) != 'undefined') {
                        this.section.data.activeBorderColorValue = result.contentBorderColor
                        needUpdate = true
                    }
                    if (typeof (result.activeButtonBorderColor) != 'undefined') {
                        this.section.data.activeButtonBorderColor = result.activeButtonBorderColor
                        needUpdate = true
                    }
                    if (needUpdate) {
                        this.sectionCtrl.update(this.section);
                    }
                }
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        setActiveFeature(feature) {
            // MANGE ACTIVE CLASS
            let currentActiveFeature;
            for (let _feature of this.section.data.features) {
                if (_feature.title) {
                    _feature.activeTitle = false;
                    _feature.bgButtonColorValue = false;
                }
            }
            feature.activeTitle = true;
            this.activeFeature = feature;

            this.sectionCtrl.update(this.section);
            this.borderAnim();
        },

        getNewFeature(wwObject) {
            let self = this;
            let feature = {
                activeTitle: false,
                bgButtonColorValue: false,
                title: wwLib.wwObject.getDefault({
                    type: "ww-text"
                }),
                contents: [wwLib.wwObject.getDefault({
                    type: "ww-text"
                })],
                uniqueId: wwLib.wwUtils.getUniqueId()
            };
            return feature;
        },

        addNewFeature(options) {
            // wrong index
            this.section.data.features.splice(
                options.index,
                0,
                this.getNewFeature(options.wwObject)
            );
            this.sectionCtrl.update(this.section);
        },
        removeFeature(options) {
            this.section.data.features.splice(options.index, 1);
            this.sectionCtrl.update(this.section);
        },

        add(list, options) {
            list.splice(options.index, 0, options.wwObject);
            this.sectionCtrl.update(this.section);
        },

        remove(list, options) {
            list.splice(options.index, 1);
            this.sectionCtrl.update(this.section);
        }
    }
};

</script>

<!-- This is your CSS -->
<!-- Add "scoped" attribute to limit CSS to this component only -->
<!-- Add lang="scss" or others to use computed CSS -->
<style lang='scss' scoped>
/*  the margin top is because the content is have a translateY(-20)*/
.feature_E {
    .background {
        position: absolute;
        width: 100%;
        height: 100%;
        top: 0;
        left: 0;
    }

    .top-ww-objs,
    .bottom-ww-objs {
        position: relative;
        .top-ww-obj,
        .bottom-ww-obj {
            position: relative;
        }
    }
    .row {
        margin-right: 10px;
        margin-left: 10px;
        margin-top: 40px;
        position: relative;

        .container-margin {
            @media (min-width: 768px) {
                left: -50%;
                transform: translateX(10%);
            }
            @media (min-width: 992px) {
                transform: translateX(30%);
            }
        }

        .container-size {
            width: 100%;

            @media (min-width: 768px) {
                width: 83.33333333%;
            }
            @media (min-width: 992px) {
                width: 66.66666667%;
            }
            @media (min-width: 1400px) {
                width: 60%;
            }
        }
        .features-container {
            display: flex;
            transition: all 0.4s ease-out;
            justify-content: center;

            .features {
                padding-top: 10px;
                flex-basis: 350px;
                @media (min-width: 768px) {
                    flex-basis: 235px;
                }
                @media (min-width: 992px) {
                    flex-basis: 205px;
                }
                @media (min-width: 1400px) {
                    flex-basis: 400px;
                }
                /* wwManager:start */

                .contextmenu {
                    position: absolute;
                    top: 0;
                    left: 0;
                    width: 30px;
                    height: 30px;
                    color: white;
                    background-color: #ef811a;
                    border-radius: 100%;
                    display: flex;
                    justify-content: center;
                    align-items: center;
                    font-size: 1.2rem;
                    cursor: pointer;
                    z-index: 2;
                }

                /* wwManager:end */

                .feature {
                    .feature-title {
                        position: relative;
                        width: 90%;
                        margin-left: 5%;
                        background-color: white;
                        padding: 10px 20px;
                        border: 1px solid rgba(82, 173, 20, 1);
                        border-top-left-radius: 20px;
                        border-bottom-left-radius: 20px;
                        border-bottom-right-radius: 20px;
                        cursor: pointer;
                        pointer-events: all;
                        transition: all 0.3s ease;
                        @media (min-width: 768px) {
                            width: 100%;
                            margin-left: 0;
                            border-right: 0;
                            border-bottom-right-radius: 0;
                        }
                    }

                    .active-mobile {
                        display: block;
                        background-color: white;
                        margin-top: -20px;
                        margin-bottom: 20px;
                        border: 1px solid rgba(82, 173, 20, 1);
                        border-radius: 4px;
                        padding: 40px 15px;
                    }
                    .xs-content {
                        @media (min-width: 768px) {
                            display: none;
                        }
                        .feature-content {
                            position: relative;
                        }
                    }
                }
            }
            .feature + .feature {
                margin-top: 40px;
            }
            .feature:last-of-type {
                margin-bottom: 50px;
            }

            .content {
                display: none;
                background-color: rgba(0, 0, 0, 0);
                width: 300px;
                border-radius: 30px;
                position: relative;
                @media (min-width: 768px) {
                    display: table-cell;
                    width: 350px;
                    transform: translateY(-20px);
                }
                @media (min-width: 992px) {
                    width: 400px;
                }
                @media (min-width: 1100px) {
                    width: 450px;
                }
                @media (min-width: 1200px) {
                    width: 500px;
                }
                @media (min-width: 1300px) {
                    width: 550px;
                }
                @media (min-width: 1400px) {
                    width: 600px;
                }
                @media (min-width: 1500px) {
                    width: 650px;
                }
                @media (min-width: 1600px) {
                    width: 700px;
                }
                @media (min-width: 1700px) {
                    width: 750px;
                }

                .content-mask {
                    border-radius: 33px;
                    overflow: hidden;
                    transform: translateZ(0);
                    height: 100%;
                    .content-bg-color {
                        position: absolute;
                        width: 50%;
                        height: 100%;
                        transform-origin: 0% 50%;
                    }
                    .confirm-border {
                        border-radius: 33px 0 0 33px;
                    }
                    .content-container {
                        background-color: white;
                        position: relative;
                        margin: 4px;
                        height: calc(100% - 8px);
                        border-radius: 30px;
                        padding: 40px;
                        box-shadow: 2px 0 5px 0 rgba(0, 0, 0, 0.2);
                        .feature-content {
                            position: relative;
                        }
                    }
                }
            }
        }
    }
}

.feature_E .feature_E .feature-title.active {
    background-color: #99d670;
    color: white;
    margin-bottom: 0;
}

.enable-animation {
    animation: content-bg-color-animation 0.6s forwards;
}

@keyframes content-bg-color-animation {
    0% {
        transform: scale(0, 0);
        opacity: 0;
    }
    50% {
        transform: scale(0.1, 1);
        opacity: 0.5;
    }

    100% {
        transform: scale(1, 1);
        opacity: 1;
    }
}
</style>

