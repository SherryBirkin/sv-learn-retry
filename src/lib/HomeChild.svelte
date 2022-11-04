<script lang="ts">
  import { createEventDispatcher }  from 'svelte';

  const dispatch = createEventDispatcher();
  
  let inputVal = '';

  const handleChangeParent = () => {
    dispatch('handleParentMethod', inputVal);
  };

  export let strFromParent = '默认空';
  export const strExpose = '本子组件暴露出去的某个属性值';
  export const methodExpose = (newVal) => {
    inputVal = newVal;
  };
</script>

<div class="child-block">
  <h3>这是子</h3>
  <div>从父组件传过来的：{strFromParent}</div>

  <div>
    <span>输入以执行父组件的方法来改变父组件的属性：</span>
    <input type="text" bind:value={inputVal}>
    <button on:click={handleChangeParent}>执行</button>
  </div>
</div>

<style scoped>
.child-block {
  background-color: #ff0;
}
</style>
