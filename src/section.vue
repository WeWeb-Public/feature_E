<template>

  <div class="feature_E">

    <!-- wwManager:start -->
    <!-- <wwSectionEditMenu :section="section"></wwSectionEditMenu> -->
    <!-- wwManager:end -->

    <div class="feature_E section-side-padding">
      <wwObject class="background"
                v-bind:ww-object="section.data.background"
                ww-category="background"></wwObject>

      <!--TOP WWOBJS-->
      <!-- marge and an add element -->
      <div class="top-ww-objs">
        <wwLayoutColumn tag='div'
                        ww-default="ww-image"
                        :ww-list="section.data.topWwObjs"
                        class="top-ww-obj">
          <wwObject v-for="topWwObj in section.data.topWwObjs"
                    :key="topWwObj.uniqueId"
                    v-bind:ww-object="topWwObj"></wwObject>
        </wwLayoutColumn>
      </div>

      <!--CONTENT-->
      <div class="row">
        <div class="col-xs-12 col-sm-10 col-sm-offset-1 col-md-8 col-md-offset-2 col-lg-8 col-lg-offset-2 features-container">
          <div class="features">
            <div v-for="(feature, index) in section.data.features"
                 :key="index">
              <div class="feature-title"
                   :class="{'active': feature.title.data.active}"
                   :style="getActiveStyle(feature.title.data.active)"
                   @click="setActiveFeature(feature)">
                <wwObject tag="div"
                          v-bind:ww-object="feature.title"
                          ww-object-types-allowed="['text']"></wwObject>
              </div>
              <div class="xs-content"
                   :class="{'active': feature.title.data.active}"
                   :style="{'border-color': section.data.activeBorderColorValue}">
                <wwObject v-for="(content, index) in section.data.activeFeature.contents"
                          :key="index"
                          class="feature-content"></wwObject>
                <wwObject tag="div"
                          v-bind:ww-object="section.data.content"></wwObject>
                <!-- <div v-if="editingSection" class="remove-ww-obj" @click="removeWwObjFromArray(data.bottomWwObjs, bottomWwObj)"><i class="fa fa-times" aria-hidden="true"></i></div> -->
              </div>
            </div>
            <!-- <div v-if="editingSection" class="remove-ww-obj" @click="removeWwObjFromArray(data.features, feature)"><i class="fa fa-times" aria-hidden="true"></i></div> -->
          </div>
          <div class="add-ww-obj-container">
            <!-- <div v-if="editingSection" class="add-ww-obj" @click="addNewFeature()"><i class="fa fa-plus" aria-hidden="true"></i></div> -->
          </div>
        </div>
        <div class="content">
          <div class="content-mask">
            <div class="clearfix"></div>
            <div class="content-bg-color confirm-border"
                 :style="{'background-color': section.data.activeBorderColorValue}"></div>
            <div class="content-container">

              <div v-for="(content, index) in section.data.activeFeature.contents"
                   :key="index"
                   class="feature-content"></div>
              <div tag="div"
                   v-bind:ww-object="section.data.content"></div>
              <!-- <div v-if="editingSection" class="remove-ww-obj" :click="removeWwObjFromArray(data.bottomWwObjs, bottomWwObj)"><i class="fa fa-times" aria-hidden="true"></i></div> -->
            </div>
            <div class="add-ww-obj-container">
              <!-- <div v-if="editingSection" class="add-ww-obj" :click="addWwObjToArray(data.activeFeature.contents)"><i class="fa fa-plus" aria-hidden="true"></i></div> -->
            </div>
          </div>
        </div>
      </div>

      <!--BOTTOM WWOBJS-->
      <div class="bottom-ww-objs">
        <wwLayoutColumn tag='div'
                        ww-default="ww-image"
                        :ww-list="section.data.bottomWwObjs"
                        class="top-ww-obj">
          <wwObject v-for="bottomWwObj in section.data.bottomWwObjs"
                    :key="bottomWwObj.uniqueId"
                    v-bind:ww-object="bottomWwObj"></wwObject>
        </wwLayoutColumn>
      </div>

    </div>
  </div>
</template>

<!-- This is your Javascript -->
<!-- ✨ Here comes the magic ✨ -->
<script>
export default {
  name: "feature_E",
  props: {
    // The section object is passed to you.
    // It contains all the info and data about the section
    // Use it has you like !
    section: Object
  },
  data() {
    return {
      editingSection: false,
      topWwObjs: null,
      bottomWwObjs: null,
      // features: null,
      //activeFeature: null,
      borderColorValue: null,
      activeBgButtonColorValue: null,
      activeBorderColorValue: null
    };
  },
  computed: {
    // section() {
    //   return this.sectionCtrl.get();
    // }
  },
  created() {
    //Initialize section data
    this.section.data = this.section.data || {};

    if (!this.section.data.background) {
      this.section.data.background = wwLib.wwObject.getDefault({
        type: "ww-image",
        data: {
          url: "http://cdn.wewebapp.io/public/images/weweb-wp.png"
        }
      });
    }

    // if (!this.section.data.content) {
    //   this.section.data.content = wwLib.wwObject.getDefault({
    //     type: "ww-text"
    //   });
    //}
    // if (!this.section.data.title) {
    //   this.section.data.title = wwLib.wwObject.getDefault({
    //     type: "ww-text"
    //   });
    // }

    if (!this.section.data.activeFeature) {
      this.section.data.activeFeature = wwLib.wwObject.getDefault({
        type: "ww-text"
      });
    }
  },
  mounted() {
    this.init();
    //this.borderAnim();
  },
  methods: {
    init() {
      this.section.data.topWwObjs = this.section.data.topWwObjs || [];
      this.section.data.bottomWwObjs = this.section.data.bottomWwObjs || [];

      this.section.data.features = this.section.data.features || [
        this.getNewFeature()
      ];

      this.section.data.activeFeature = this.section.data.features[0];
      this.section.data.borderColorValue =
        this.section.data.borderColorValue || "#54AB26";
      this.section.data.activeBgButtonColorValue =
        this.section.data.activeBgButtonColorValue || "#9AD575";
      this.section.data.activeBorderColorValue =
        this.section.data.activeBorderColorValue || "#54AB26";

      this.setActiveFeature(this.section.data.activeFeature);

      // CUSTOM OPTIONS (colors)
      // setInterval(function() {
      //   registerOptionsPopup(this.openCustomPopup);
      // }, 1);

      // wwLib.wwPopup.registerStory({
      //   POPUP_FEATURE_E_COLORS: {
      //     title: wwLib.wwManagerLang.get("popups.CONFIGURATION.configuration"),
      //     type: wwLib.wwPopupStory.types.FORM,
      //     buttons: [
      //       {
      //         text: wwLib.wwManagerLang.get("popups.ok"),
      //         class: [
      //           "ww-footer-btn btn-animated-icon ww-font-title ww-btn-bg-red"
      //         ],
      //         action: {
      //           type: wwLib.wwPopupStory.buttonActions.RETURN
      //         }
      //       }
      //     ],
      //     data: {
      //       BORDER_COLOR_VALUE: {
      //         type: wwLib.wwPopupStory.formTypes.COLOR,
      //         text: wwLib.wwManagerLang.get("sections.feature_E.borderColor"),
      //         key: "borderColorValue"
      //       },
      //       ACTIVE_BORDER_COLOR_VALUE: {
      //         type: wwLib.wwPopupStory.formTypes.COLOR,
      //         text: wwLib.wwManagerLang.get(
      //           "sections.feature_E.activeBorderColor"
      //         ),
      //         key: "activeBorderColorValue"
      //       },
      //       BG_BUTTON_COLOR_VALUE: {
      //         type: wwLib.wwPopupStory.formTypes.COLOR,
      //         text: wwLib.wwManagerLang.get(
      //           "sections.feature_E.activeBgButtonColor"
      //         ),
      //         key: "activeBgButtonColorValue"
      //       }
      //     }
      //   }
      // });

      // REGISTER ANIMATIONS
      // setInterval(function() {
      //   wwLib.wwCustomCSS.registerScrollElement({
      //     element: this.$el.querySelector(".content-bg-color"),
      //     fn: this.borderAnim()
      //   });
      // }, 1);

      this.$emit("ctrl-ready", "feature_E");
    },
    openCustomPopup() {
      var options = {};

      options.columnCount = parseInt(this.data.columnCount) + 0;

      options.firstPage = "POPUP_FEATURE_E_COLORS";

      options.wwObject = { content: { data: {} } };
      options.wwObject.content.data.activeBgButtonColorValue = this.data.activeBgButtonColorValue;
      options.wwObject.content.data.borderColorValue = this.data.borderColorValue;
      options.wwObject.content.data.unactiveBorderColorValue = this.data.unactiveBorderColorValue;

      wwLib.wwPopup
        .open(options)
        .then(function(result) {
          console.log("LE RESULTAT !!!", result);

          var borderColorValue = wwLib.wwPopup.getValue(
            result,
            "borderColorValue"
          );
          if (borderColorValue) {
            this.data.borderColorValue = borderColorValue;
          }

          var activeBorderColorValue = wwLib.wwPopup.getValue(
            result,
            "activeBorderColorValue"
          );
          if (activeBorderColorValue) {
            this.data.activeBorderColorValue = activeBorderColorValue;
          }

          var activeBgButtonColorValue = wwLib.wwPopup.getValue(
            result,
            "activeBgButtonColorValue"
          );
          if (activeBgButtonColorValue) {
            this.data.activeBgButtonColorValue = activeBgButtonColorValue;
          }
        })
        .catch(function(error) {
          console.log("ERROR : ", error);
        });
    },

    borderAnim() {
      let sectionElement = this.$el;

      // setInterval(function() {
      //   sectionElement
      //     .getElementsByClassName(".content-bg-color")
      //     .removeClass("content-bg-color-anim1");
      //   sectionElement
      //     .getElementsByClassName(".content-bg-color")
      //     .removeClass("content-bg-color-anim2");
      //   setInterval(function() {
      //     sectionElement
      //       .getElementsByClassName(".content-bg-color")
      //       .addClass("content-bg-color-anim1");
      //     setInterval(function() {
      //       sectionElement
      //         .getElementsByClassName(".content-bg-color")
      //         .addClass("content-bg-color-anim2");
      //     }, 400);
      //   }, 1);
      // }, 1);
    },

    setActiveFeature(feature) {
      // MANGE ACTIVE CLASS
      var currentActiveFeature;
      for (var _feature of this.section.data.features) {
        if (_feature.title && _feature.title.data.active) {
          _feature.title.data.active = false;
          _feature.title.data.bgButtonColorValue = false;
        }
      }
      feature.title.data.active = true;
      this.section.data.activeFeature = feature;
      //this.borderAnim();
    },

    removeWwObjFromArray(wwObjArray, index) {
      if (index !== -1) {
        wwObjArray.splice(index, 1);
      }
    },

    addWwObjToArray(wwObjArray, option) {
      var objToPush = this.getNewWwObj();
      if (option === "empty") {
        objToPush = {};
      }
      wwObjArray.push(objToPush);
    },

    getNewWwObj() {
      return {
        data: {},
        link: { data: {}, type: "none" },
        ratio: 66.66666,
        hidden: false,
        content: {
          data: {
            tag: "div",
            font: "",
            size: "",
            text: { fr_FR: "Mon titre" },
            align: "center",
            color: "",
            classes: [],
            children: []
          },
          type: "ww-text"
        },
        children: {},
        uniqueId: wwLib.wwUtils.getUniqueId(),
        wwVersion: 2
      };
    },

    getNewFeature() {
      let self = this;
      let feature = {
        title: self.getNewWwObj(),
        contents: [self.getNewWwObj()],
        uniqueId: wwLib.wwUtils.getUniqueId()
      };
      return feature;
    },
    addNewFeature() {
      this.section.data.features.push(this.getNewFeature());
    },
    getActiveStyle(active) {
      if (active) {
        return {
          "background-color": this.section.data.activeBgButtonColorValue,
          "border-color": this.section.data.activeBorderColorValue
        };
      } else {
        return {
          "background-color": "#FFFFFF",
          "border-color": this.section.data.borderColorValue
        };
      }
    }
  }
};
</script>

<!-- This is your CSS -->
<!-- Add "scoped" attribute to limit CSS to this component only -->
<!-- Add lang="scss" or others to use computed CSS -->
<style scoped>
.feature_E .container {
  width: 100%;
  height: 100%;
  position: relative;
  padding: 0px;
}

.feature_E .background {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}

.feature_E .top-ww-objs,
.feature_E .bottom-ww-objs {
  position: relative;
}

.feature_E .top-ww-obj,
.feature_E .bottom-ww-obj {
  position: relative;
}

.feature_E .remove-ww-obj {
  position: absolute;
  top: 0;
  left: 0;
  transform: translate(-50%, -50%);
  height: 30px;
  width: 30px;
  background-color: white;
  color: #92979c;
  box-shadow: 0px 1px 1px 0px #656565;
  -moz-box-shadow: 0px 1px 1px 0px #656565;
  -webkit-box-shadow: 0px 1px 1px 0px #656565;
  border-radius: 100%;
  -moz-border-radius: 100%;
  -webkit-border-radius: 100%;
  text-align: center;
  line-height: 30px;
  cursor: pointer;
  z-index: 2;
}

.feature_E .add-ww-obj-container {
  text-align: center;
  margin: 10px 0;
}

.feature_E .add-ww-obj-container .add-ww-obj {
  display: inline-block;
  height: 40px;
  width: 40px;
  line-height: 40px;
  text-align: center;
  background-color: white;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.5);
  border-radius: 3px;
  -webkit-border-radius: 3px;
  -moz-border-radius: 3px;
  cursor: pointer;
}

.feature_E .features-container {
  display: table;
  -moz-transition: all 0.4s ease-out;
  -o-transition: all 0.4s ease-out;
  -ms-transition: all 0.4s ease-out;
  -webkit-transition: all 0.4s ease-out;
  transition: all 0.4s ease-out;
}

.feature_E .features-container .features {
  display: table-cell;
  padding-top: 10px;
  /*vertical-align: middle;*/
}

.feature_E .feature-title {
  position: relative;
  width: 90%;
  margin-left: 5%;
  background-color: white;
  margin-bottom: 20px;
  padding: 10px 20px;
  border: 1px solid rgba(82, 173, 20, 1);
  border-top-left-radius: 20px;
  -webkit-border-top-left-radius: 20px;
  -moz-border-top-left-radius: 20px;
  border-bottom-left-radius: 20px;
  -webkit-border-bottom-left-radius: 20px;
  -moz-border-bottom-left-radius: 20px;
  -webkit-border-top-right-radius: 20px;
  -moz-border-top-right-radius: 20px;
  border-bottom-right-radius: 20px;
  -webkit-border-bottom-right-radius: 20px;
  -moz-border-bottom-right-radius: 20px;
  cursor: pointer;
}

.feature_E .feature-title.active {
  background-color: #99d670;
  color: white;
  margin-bottom: 0;
}

.feature_E .features-container .content {
  display: none;
  background-color: rgba(0, 0, 0, 0);
  width: 300px;
  border-radius: 30px;
  -moz-border-radius: 30px;
  -webkit-border-radius: 30px;
  position: relative;
}

.feature_E .content-mask {
  border-radius: 33px;
  overflow: hidden;
  transform: translateZ(0);
}

.feature_E .features-container .content-container {
  background-color: white;
  position: relative;
  margin: 4px;
  height: 100%;
  min-height: 300px;
  border-radius: 30px;
  -moz-border-radius: 30px;
  -webkit-border-radius: 30px;
  padding: 40px;
  box-shadow: 2px 0 5px 0 rgba(0, 0, 0, 0.2);
}

.feature_E .content-bg-color {
  position: absolute;
  background-color: #51ad14;
  width: 50%;
  height: 100%;
  transform: scale(0, 0);
  transform-origin: 0% 50%;
}

.feature_E .content-bg-color-anim1 {
  transform: scale(0.1, 1);
  -webkit-transition: all 0.4s ease-in;
  -moz-transition: all 0.4s ease-in;
  -o-transition: all 0.4s ease-in;
  -ms-transition: all 0.4s ease-in;
  transition: all 0.4s ease-in;
}

.feature_E .content-bg-color-anim2 {
  transform: scale(1, 1);
  -webkit-transition: all 0.4s ease-out;
  -moz-transition: all 0.4s ease-out;
  -o-transition: all 0.4s ease-out;
  -ms-transition: all 0.4s ease-out;
  transition: all 0.4s ease-out;
}

.feature_E .feature-content {
  position: relative;
}

.feature_E .xs-content {
  display: none;
}

.feature_E .xs-content.active {
  display: block;
  background-color: white;
  display: block;
  background-color: white;
  margin-top: -20px;
  margin-bottom: 20px;
  border: 1px solid rgba(82, 173, 20, 1);
  border-radius: 4px;
  -moz-border-radius: 4px;
  -webkit-border-radius: 4px;
  padding: 40px 15px;
}

.feature_E .confirm-border {
  border-radius: 33px;
  -moz-border-radius: 33px;
  -webkit-border-radius: 33px;
}

@media (min-width: 768px) {
  .feature_E .xs-content {
    display: none;
  }
  .feature_E .xs-content.active {
    display: none;
  }
  .feature_E .features-container .content {
    display: table-cell;
  }
  .feature_E .feature-title {
    width: 100%;
    margin-left: 0;
    margin-bottom: 40px;
    border-right: 0;
    -webkit-border-top-right-radius: 0;
    -moz-border-top-right-radius: 0;
    border-bottom-right-radius: 0;
    -webkit-border-bottom-right-radius: 0;
    -moz-border-bottom-right-radius: 0;
  }
  .feature_E .feature-title.active {
    margin-bottom: 40px;
  }
  .feature_E .features-container .content {
    width: 350px;
    transform: translateY(-20px);
  }
}

@media (min-width: 992px) {
  .feature_E .features-container .content {
    width: 400px;
  }
}

@media (min-width: 1100px) {
  .feature_E .features-container .content {
    width: 450px;
  }
}

@media (min-width: 1200px) {
  .feature_E .features-container .content {
    width: 500px;
  }
}

@media (min-width: 1300px) {
  .feature_E .features-container .content {
    width: 550px;
  }
}

@media (min-width: 1400px) {
  .feature_E .features-container .content {
    width: 600px;
  }
}

@media (min-width: 1500px) {
  .feature_E .features-container .content {
    width: 650px;
  }
}

@media (min-width: 1600px) {
  .feature_E .features-container .content {
    width: 700px;
  }
}

@media (min-width: 1700px) {
  .feature_E .features-container .content {
    width: 750px;
  }
}
</style>

