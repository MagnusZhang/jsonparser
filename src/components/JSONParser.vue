<template>
  <div style="display:flex;flex-direction:column;">
    <div style="margin-right:auto;">
      <div
        @click="refresh"
        class="noselect"
        style="width:64px;height: 32px;font-size: 14px; text-align:center; line-height: 32px; background:#1E9FFF; color:white; border-radius:5px;cursor:pointer;focusable:none;"
      >Format</div>
    </div>
    <div
      style="display:flex; flex-direction:row; flex-wrap:nowrap;margin:0;padding:0;background:white;margin-top:20px;"
    >
      <div style="width:29vw; height:70vh; display:flex; flex-direction:column;">
        <textarea
          style="width: 29vw; font-size:12px;height: 70vh;text-align:start; resize:none;color:#945DAA;"
          placeholder="将JSON复制到此"
          v-model="jsonOriginal"
        />
      </div>
      <div style="width: 1vw; height: 70vh; line-height: 70vh; text-align: center; margin-left: 20px;">&#62;&#62;</div>
      <div
        style="height:70vh; width: 60vw; text-align: left; margin:0; padding:0; margin-left:20px; border:1px solid rgba(0,0,0,0.5); overflow-y:scroll; overflow-x:scroll;"
      >
        <div v-if="parsedObject" style="margin-left:20px;">
          {
          <component
            v-for="key in Object.keys(parsedObject)"
            :key="key"
            :is="TreeNode"
            :children="{value:parsedObject[key]}"
            :name="key"
          ></component>}
        </div>
        <div v-else style="margin-left:5px;">JSON不合法</div>
      </div>
    </div>
  </div>
</template>
<script>
import TreeNode from "@/components/TreeNode";
export default {
  data() {
    return {
      jsonOriginal: "",
      parsedObject: null,
      TreeNode: TreeNode,
    };
  },
  watch: {
    jsonOriginal: {
      handler(value) {
        try {
          this.parsedObject = JSON.parse(value);
        } catch (e) {
          this.parsedObject = null;
        }
      },
      immediate: true,
      deep: true,
    },
  },
  methods: {
    refresh() {
      let orginal = this.jsonOriginal;
      this.jsonOriginal = null;
      let that = this;
      this.$nextTick(()=>{
        try {
          this.jsonOriginal = that.jsonParser(orginal);
        } catch(e) {
          this.jsonOriginal = orginal;
        }
      })
    },
    jsonParser(originalJson){
      var resultString = "";
      let that = this;
      var prefixSpace = "  ";
      try {
        var jsonObject = JSON.parse(originalJson);
        resultString = "{";
        var length = Object.keys(jsonObject).length;
        var count = 0;
        Object.keys(jsonObject).forEach(key=>{
          var object = jsonObject[key];
          count ++;
          var afterFix = count == length ? "":","
          if (object == null){
            resultString += "\n" + prefixSpace + "\"" + key + "\": " + object + afterFix;
          } else if (typeof(object) == "string"){
            resultString += "\n" + prefixSpace + "\"" + key + "\": \"" + object.replace(/\r\n/g,"") + "\"" + afterFix;
          } else if (typeof(object) == "number" || typeof(object) == "boolean") {
            resultString += "\n" + prefixSpace + "\"" + key + "\": " + object + afterFix;
          } else if (Array.isArray(object)) {
            resultString += "\n" + prefixSpace + "\"" + key + "\": " + that.getChildArray(object,prefixSpace) + afterFix;
          }else if (typeof(object) == "object") {
            resultString += "\n" + prefixSpace + "\"" + key + "\": " + that.getChildString(object,prefixSpace) + afterFix;
          } 
        });
        resultString += "\n}"; 
      } catch(e) {
        resultString = originalJson;
      }
      return resultString;
    },
    getChildString(jsonObject,prefixSpace) {
      var newPrefix = prefixSpace + "  ";
      var resultString = "";
      let that = this;
      try {
        var length = Object.keys(jsonObject).length;
        var count = 0;
        resultString = "{";
        Object.keys(jsonObject).forEach(key=>{
          var object = jsonObject[key];
          count ++;
          var afterFix = count == length ? "":","
          if (object == null){
            resultString += "\n" + newPrefix + "\"" + key + "\": " + object + afterFix;
          } else if (typeof(object) == "string"){
            resultString += "\n" + newPrefix + "\"" + key + "\": \"" + object.replace(/\r\n/g,"") + "\"" + afterFix;
          } else if (typeof(object) == "number" || typeof(object) == "boolean") {
            resultString += "\n" + newPrefix + "\"" + key + "\": " + object + afterFix;
          } else if (Array.isArray(object)) {
            resultString += "\n" + newPrefix + "\"" + key + "\": " + that.getChildArray(object,newPrefix) + afterFix;
          } else if (typeof(object) == "object") {
            resultString += "\n" + newPrefix + "\"" + key + "\": " + that.getChildString(object,newPrefix) + afterFix;
          } 
        });
        resultString += ("\n"+prefixSpace+"}"); 
      } catch(e) {
        resultString = JSON.stringify(jsonObject);
      }
      return resultString;
    },
    getChildArray(jsonObject,prefixSpace) {
      var newPrefix = prefixSpace + "  ";
      var resultString = "";
      let that = this;
      try {
        resultString = "[";
        var length  = jsonObject.length;
        var count = 0;
        jsonObject.forEach(object=>{
          count ++;
          var afterFix = count == length ? "":","
          if (object == null){
            resultString += "\n" + newPrefix + object + afterFix;
          } else if (typeof(object) == "string"){
            resultString += "\n" + newPrefix + "\"" + object.replace(/\r\n/g,"") + "\"" + afterFix;
          } else if (typeof(object) == "number" || typeof(object) == "boolean") {
            resultString += "\n" + newPrefix + "\"" + object + afterFix;
          } else if (Array.isArray(object)) {
            resultString += "\n" + newPrefix + that.getChildArray(object,newPrefix) + afterFix;
          }else if (typeof(object) == "object") {
            resultString += "\n" + newPrefix + that.getChildString(object,newPrefix) + afterFix;
          } 
        });
        resultString += ("\n"+prefixSpace+"]"); 
      } catch(e) {
        resultString = JSON.stringify(jsonObject);
      }
      return resultString;
    }
  },
};
</script>
<style scoped>
.black-border {
  border: 1px;
  border-color: black;
}
.noselect {
  -webkit-touch-callout: none; /* iOS Safari */

  -webkit-user-select: none; /* Chrome/Safari/Opera */

  -khtml-user-select: none; /* Konqueror */

  -moz-user-select: none; /* Firefox */

  -ms-user-select: none; /* Internet Explorer/Edge */

  user-select: none; /* Non-prefixed version, currently

not supported by any browser */
}
</style>