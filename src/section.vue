<template>
    <div class="feature_E" :style="customStyle">
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
                        <wwLayoutColumn tag="div" ww-default="ww-text" :ww-list="section.data.features">
                            <div v-for="(feature, index) in section.data.features" :key="feature.uniqueId" class="feature">
                                <!-- wwManager:start -->
                                <wwContextMenu
                                    tag="div"
                                    class="contextmenu"
                                    v-if="editMode"
                                    @ww-add-before="addFeature(index, 'before')"
                                    @ww-add-after="addFeature(index, 'after')"
                                    @ww-remove="removeFeature(index)"
                                >
                                    <div class="wwi wwi-config"></div>
                                </wwContextMenu>
                                <!-- wwManager:end -->
                                <div class="feature-title" :class="{'active': feature.activeTitle}" @click="setActiveFeature(feature)">
                                    <wwObject tag="div" :ww-object="feature.title" ww-object-types-allowed="['text']"></wwObject>
                                </div>
                                <div class="xs-content" :class="{'active-mobile': feature.activeTitle}">
                                    <div v-for="content in activeFeature.contents" :key="content.uniqueId" class="feature-content">
                                        <wwObject tag="div" :ww-object="content"></wwObject>
                                    </div>
                                </div>
                            </div>
                        </wwLayoutColumn>
                    </div>
                    <div class="content">
                        <div class="content-mask">
                            <div class="content-bg-color confirm-border"></div>

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
    name: '__COMPONENT_NAME__',
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
        customStyle() {
            return {
                '--bannerBorderRadius': this.section.data.bannerBorderRadius,
                '--mobileContentBorderRadius': this.section.data.mobileContentBorderRadius,
                '--titleBorderRadius': this.section.data.titleBorderRadius,
                '--animationBorderRadius': this.section.data.animationBorderRadius,
                '--shadowConfig': this.section.data.shadowConfig,

                '--defaultButtonColor': this.section.data.defaultButtonColor,
                '--contentBackground': this.section.data.contentBackground,
                '--activeBorderColorValue': this.section.data.activeBorderColorValue,
                '--activeButtonColor': this.section.data.activeButtonColor,
            }
        }
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
            wwLib.wwUtils.changeUniqueIds(this.section.data.activeFeature)

            this.section.data.defaultButtonColor =
                this.section.data.defaultButtonColor || '#FFFFFF';

            this.section.data.contentBackground =
                this.section.data.contentBackground || '#FFFFFF';

            this.section.data.activeButtonColor =
                this.section.data.activeButtonColor || '#54AB26';

            this.section.data.activeBorderColorValue =
                this.section.data.activeBorderColorValue || '#54AB26';

            /* shadow and border radius */
            this.section.data.shadowConfig =
                this.section.data.shadowConfig || '0 10px 40px 0 rgba(113, 124, 137, 0.2)';

            this.section.data.bannerBorderRadius =
                this.section.data.bannerBorderRadius || '30px';

            this.section.data.mobileContentBorderRadius =
                this.section.data.mobileContentBorderRadius || '4px';

            this.section.data.titleBorderRadius =
                this.section.data.titleBorderRadius || '20px 0 0 20px';

            this.section.data.animationBorderRadius =
                this.section.data.animationBorderRadius || '33px 0 0 33px';

            this.section.data = this.section.data || {};

            if (!this.section.data.background) {
                this.section.data.background = wwLib.wwObject.getDefault({
                    type: 'ww-image',
                    data: {
                        url:
                            'https://wewebdev.s3-eu-west-1.amazonaws.com/public/images/urban/ChicagoSkyline1.jpg'
                    }
                });
                needUpdate = true;
            }

            if (!this.section.data.content) {
                this.section.data.content = wwLib.wwObject.getDefault({
                    type: 'ww-text'
                });
                needUpdate = true;
            }
            if (!this.section.data.title) {
                this.section.data.title = wwLib.wwObject.getDefault({
                    type: 'ww-text'
                });
                needUpdate = true;
            }

            if (!this.section.data.activeFeature) {
                this.section.data.activeFeature = wwLib.wwObject.getDefault({
                    type: 'ww-text'
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
            this.$emit('ctrl-ready', 'feature_E');
        },

        borderAnim() {
            let sectionElement = this.$el;

            if (this.$el) {
                setTimeout(function () {
                    sectionElement
                        .querySelector('.content-bg-color')
                        .classList.remove('enable-animation');
                    setTimeout(function () {
                        sectionElement
                            .querySelector('.content-bg-color')
                            .classList.add('enable-animation');
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
                                    en: 'Title border radius:',
                                    fr: 'Arrondis des coins du titre :'
                                },
                                type: 'text',
                                key: 'titleBorderRadius',
                                valueData: 'section.data.titleBorderRadius',
                                desc: {
                                    en: 'Title border radius in order top-left | top-right | bottom-right | bottom-left or a single value for all',
                                    fr: 'Arrondis des coins du titre dans l\'ordre en haut à gauche | en haut à droite | en bas à droite | en bas à gauche ou une seule valeur pour tous :'
                                }
                            },
                            {
                                label: {
                                    en: 'Default button color:',
                                    fr: 'Couleur par default du bouton :'
                                },
                                type: 'color',
                                key: 'defaultButtonColor',
                                valueData: 'section.data.defaultButtonColor',
                                desc: {
                                    en: 'Choose the default color of the button border color:',
                                    fr: 'Changer la couleur par default de la bordure du bouton :'
                                },
                            },
                            {
                                label: {
                                    en: 'Active button color:',
                                    fr: 'Couleur du bouton actif :'
                                },
                                type: 'color',
                                key: 'activeButtonColor',
                                valueData: 'section.data.activeButtonColor',
                                desc: {
                                    en: 'Choose the color of the button after a clic:',
                                    fr: 'Changer la couleur du bouton après un clic :'
                                },
                            },
                            {
                                label: {
                                    en: 'Banner shadow config:',
                                    fr: 'Configuration de l\'ombre de la bannière :'
                                },
                                type: 'text',
                                key: 'shadowConfig',
                                valueData: 'section.data.shadowConfig',
                                desc: {
                                    en: 'Box-shadow of the banner: offset-x | offset-y | blur-radius | spread-radius | color',
                                    fr: 'L\'ombre de la bannière : offset-x | offset-y | blur-radius | spread-radius | color'
                                }
                            },
                            {
                                label: {
                                    en: 'Border radius:',
                                    fr: 'Arrondis des coins :'
                                },
                                type: 'text',
                                key: 'bannerBorderRadius',
                                valueData: 'section.data.bannerBorderRadius',
                                desc: {
                                    en: 'Banner border radius: in order top-left | top-right | bottom-right | bottom-left or a single value for all ',
                                    fr: 'La bordure des coins de la bannière : dans l\'ordre en haut à gauche | en haut à droite | en bas à droite | en bas à gauche ou une seule valeur pour tous :'
                                }
                            },
                            {
                                label: {
                                    en: 'Banner border radius:',
                                    fr: 'Arrondis des coins:'
                                },
                                type: 'text',
                                key: 'mobileContentBorderRadius',
                                valueData: 'section.data.mobileContentBorderRadius',
                                desc: {
                                    en: 'Banner border radius on mobile in order top-left | top-right | bottom-right | bottom-left or a single value for all ',
                                    fr: 'Arrondis des coins de la bannière sur mobile dans l\'ordre en haut à gauche | en haut à droite | en bas à droite | en bas à gauche ou une seule valeur pour tous :'
                                }
                            },

                            {
                                label: {
                                    en: 'Animation border radius:',
                                    fr: 'Arrondis des coins de l\'animation :'
                                },
                                type: 'text',
                                key: 'animationBorderRadius',
                                valueData: 'section.data.animationBorderRadius',
                                desc: {
                                    en: 'Example: 33px 0 0 33px, in order top-left | top-right | bottom-right | bottom-left or a single value for all',
                                    fr: 'Exemple: 33px 0 0 33px dans l\'ordre en haut à gauche | en haut à droite | en bas à droite | en bas à gauche ou une seule valeur pour tous '
                                }
                            },
                            {
                                label: {
                                    en: 'Banner border color:',
                                    fr: 'Couleur de la bordure de la bannière :'
                                },
                                type: 'color',
                                key: 'contentBorderColor',
                                valueData: 'section.data.activeBorderColorValue',
                                desc: {
                                    en: 'Choose the border color of the banner animation',
                                    fr: 'Couleur de la bordure de la bannière pour l\'animation'
                                },
                            },

                            {
                                label: {
                                    en: 'Banner background color:',
                                    fr: 'Couleur de fond de la bannière :'
                                },
                                type: 'color',
                                key: 'contentBackground',
                                valueData: 'section.data.contentBackground',
                                desc: {
                                    en: 'The background color of the banner',
                                    fr: 'Couleur du background de la bannière:'
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
                    if (typeof (result.defaultButtonColor) != 'undefined') {
                        this.section.data.defaultButtonColor = result.defaultButtonColor
                        needUpdate = true
                    }

                    if (typeof (result.contentBorderColor) != 'undefined') {
                        this.section.data.activeBorderColorValue = result.contentBorderColor
                        needUpdate = true
                    }
                    if (typeof (result.contentBackground) != 'undefined') {
                        this.section.data.contentBackground = result.contentBackground
                        needUpdate = true
                    }

                    if (typeof (result.activeButtonColor) != 'undefined') {
                        this.section.data.activeButtonColor = result.activeButtonColor
                        needUpdate = true
                    }

                    if (typeof (result.shadowConfig) != 'undefined') {
                        this.section.data.shadowConfig = result.shadowConfig;
                        needUpdate = true;
                    }

                    if (typeof (result.bannerBorderRadius) != 'undefined') {
                        this.section.data.bannerBorderRadius = result.bannerBorderRadius;
                        needUpdate = true;
                    }
                    if (typeof (result.mobileContentBorderRadius) != 'undefined') {
                        this.section.data.mobileContentBorderRadius = result.mobileContentBorderRadius;
                        needUpdate = true;
                    }

                    if (typeof (result.titleBorderRadius) != 'undefined') {
                        this.section.data.titleBorderRadius = result.titleBorderRadius;
                        needUpdate = true;
                    }
                    if (typeof (result.animationBorderRadius) != 'undefined') {
                        this.section.data.animationBorderRadius = result.animationBorderRadius;
                        needUpdate = true;
                    }

                    if (needUpdate) {
                        this.sectionCtrl.update(this.section);
                        this.$forceUpdate();

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

        getNewFeature() {
            let self = this;
            let feature = {
                activeTitle: false,
                bgButtonColorValue: false,
                title: wwLib.wwObject.getDefault({
                    type: 'ww-text'
                }),
                contents: [wwLib.wwObject.getDefault({
                    type: 'ww-text'
                })],
                uniqueId: wwLib.wwUtils.getUniqueId()
            };
            return feature;
        },
        addFeature(_index, where) {
            console.log('where:', where)
            console.log('_index:', _index)
            try {
                const up = (where == 'after') ? parseInt(1) : 0;
                const index = _index + up
                const newCard = this.getNewFeature()

                this.section.data.features.splice(index, 0, newCard);
                this.sectionCtrl.update(this.section);
            } catch (err) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        addNewFeature(options) {
            // wrong index
            this.section.data.features.splice(
                options.index,
                0,
                this.getNewFeature()
            );
            this.sectionCtrl.update(this.section);
        },
        removeFeature(index) {
            try {
                this.section.data.features.splice(index, 1);
                if (!this.section.data.features.length) {
                    this.addFeature(0, 'after');
                }
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);

            }
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
<!-- Add 'scoped' attribute to limit CSS to this component only -->
<!-- Add lang='scss' or others to use computed CSS -->
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
        margin-top: 60px;
        margin-bottom: 20px;
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
                    left: 0;
                    width: 30px;
                    height: 30px;
                    color: white;
                    background-color: #ef811a;
                    transform: translate(-50%, -50%);
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
                        background-color: var(--defaultButtonColor);
                        padding: 10px 20px;
                        border: 1px solid;
                        border-color: var(--activeButtonColor);
                        border-radius: var(--titleBorderRadius);
                        cursor: pointer;
                        pointer-events: all;
                        transition: all 0.3s ease;
                        @media (min-width: 768px) {
                            width: 100%;
                            margin-left: 0;
                            border-right: 0;
                            border-bottom-right-radius: 0;
                            border-top-right-radius: 0;
                        }
                    }
                    .active {
                        background-color: var(--activeButtonColor);
                        color: white;
                    }

                    .xs-content {
                        display: none;
                        .feature-content {
                            position: relative;
                        }
                    }
                    .active-mobile {
                        display: block;
                        background-color: var(--contentBackground);
                        margin-top: -20px;
                        margin-bottom: 20px;
                        border: 1px solid;
                        border-color: var(--activeButtonColor);
                        border-radius: var(--mobileContentBorderRadius);
                        padding: 40px 15px;
                        @media (min-width: 768px) {
                            display: none;
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
                min-height: 300px;
                //border-radius: 30px;
                border-radius: var(--bannerBorderRadius);
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
                    border-radius: var(--bannerBorderRadius);
                    overflow: hidden;
                    transform: translateZ(0);
                    height: 100%;
                    .content-bg-color {
                        background-color: var(--activeBorderColorValue);
                        position: absolute;
                        width: 50%;
                        height: 100%;
                        transform-origin: 0% 50%;
                    }
                    .confirm-border {
                        border-radius: var(--animationBorderRadius);
                    }

                    .content-container {
                        position: relative;
                        background-color: var(--contentBackground);
                        margin: 4px;
                        height: calc(100% - 8px);
                        border-radius: var(--bannerBorderRadius);
                        padding: 40px;
                        box-shadow: var(--shadowConfig);
                        .feature-content {
                            position: relative;
                        }
                    }
                }
            }
        }
    }
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

