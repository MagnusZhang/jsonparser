<template>
  <div
    style="display:flex; flex-direction:row; flex-wrap:nowrap; margin-left: 20px;cursor:pointer; background:white;font-size:12px;"
  >
    <div style="color:#204a87;font-size:12px;">{{mName}}:</div>
    <div
      v-if="typeof(mChildren) != 'string' && typeof(mChildren) != 'number' && typeof(mChildren) != 'boolean' && mChildren!=null && mChildren!='null'"
      style="color:red;font-size:10px;"
      @click="changeFolded"
    >{{folded ? '[+]':'[-]'}}&nbsp;</div>
    <div v-else>&nbsp;</div>
    <div
      v-if="folded && typeof(mChildren) != 'string' && typeof(mChildren) != 'number' && typeof(mChildren) != 'boolean' && mChildren!=null && mChildren!='null'"
      @click="changeFolded"
    >
      <div v-if="!Array.isArray(mChildren)" style="color:#4e9a06;">{...}</div>
      <div v-if="Array.isArray(mChildren)" style="color:#4e9a06;">[...] <span style="color:#737373;">Array({{mChildren.length}})</span></div>
    </div>
    <div v-else>
      <div v-if="mChildren == null || mChildren=='null'">null</div>
      <div
        v-else-if="typeof(mChildren) == 'string' || typeof(mChildren) == 'number' || typeof(mChildren) == 'boolean'"
        style="display:flex;flex-direction:row; flex-wrap:nowrap;"
      >
        <div v-if="typeof(mChildren) == 'string'" style="color:#4e9a06;">"{{mChildren}}"</div>
        <div v-if="typeof(mChildren) != 'string'" style="color:#4e9a06;">{{mChildren}}</div>,
      </div>
      <div v-else-if="Array.isArray(mChildren)">
        [
        <div
          v-if="mChildren && Object.keys(mChildren).length"
          style="display:flex; flex-direction:column; flex-wrap:nowrap;"
        >
          <component
            v-for="key in Object.keys(mChildren)"
            :key="key"
            :is="TreeNode"
            :children="{value:mChildren[key]}"
            :name="key"
          ></component>
        </div>],
      </div>
      <div v-else>
        {
        <div
          v-if="mChildren && Object.keys(mChildren).length"
          style="display:flex; flex-direction:column; flex-wrap:nowrap;"
        >
          <component
            v-for="key in Object.keys(mChildren)"
            :key="key"
            :is="TreeNode"
            :children="{value:mChildren[key]}"
            :name="key"
          ></component>
        </div>},
      </div>
    </div>
  </div>
</template>
<script>
import TreeNode from "@/components/TreeNode";
export default {
  data() {
    return {
      mChildren: null,
      mName: null,
      TreeNode: TreeNode,
      folded: true,
    };
  },
  props: {
    children: Object,
    name: String,
  },
  created() {
    this.mChildren = this.children.value;
    this.mName = this.name;
  },
  methods: {
    changeFolded() {
      this.folded = !this.folded;
    }
  },
};
</script>
<style scoped>
</style>