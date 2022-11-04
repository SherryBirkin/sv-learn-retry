<script lang="ts">
import { onMount, tick } from 'svelte';
import HomeChildSv from '@/lib/HomeChild.svelte';

const htmlStr = '<h3>这是html字符串，用来渲染html</h3>';
const colorRed = '#f00';

let inputTypeOpts = [
  {
    typeName: '单行文本',
    typeVal: 'text',
  },
  {
    typeName: '数字条',
    typeVal: 'range',
  },
  {
    typeName: '单选',
    typeVal: 'radio',
  },
  {
    typeName: '多选',
    typeVal: 'checkbox',
  }
];
let selectedInputType = '';
let selectedMultiInputTypesList = [];
let origDom = null;
let isActive = false;
let restCount = 5;
let tickStr = 'tick还没执行';
let strFromChild = '无';
let homeChildDOM = null;

// const handleTriggerPromClick = () => {
//   setTimeout(() => {
//     if (restCount > 0) {
//       restCount -= 1;
//     } else {

//     }
//   }, 1000);
// };

const prom = new Promise((resolve) => {
  const timer = setInterval(async () => {
    if (restCount > 1) {
      restCount -= 1;
    } else {
      clearInterval(timer);
      resolve('异步已完成');
      Promise.resolve().then(() => {
        tickStr = 'tick执行wei任务';
      });
      await tick();
      tickStr = 'tick执行完毕';
    }
  }, 1000);
});

const changeByChild = (arg) => {
  console.log('子执行父组件方法');
  
  const { detail } = arg;

  strFromChild = detail;
};

const getChildProperty = () => {
  console.log('获取到子组件暴露出来的属性：', homeChildDOM.strExpose);
  homeChildDOM.methodExpose('父执行了子暴露的方法');
};

onMount(() => {
  setTimeout(() => {
    if (origDom) {
      origDom.innerText = '原生DOM改变了';
    }
  }, 4000);
});
</script>

<div>
  <h4>Home</h4>

  <div>
    <input type={selectedInputType}>

    {#each inputTypeOpts as { typeVal, typeName }, ind}
      <div>
        <label>
          <input
            type="radio"
            bind:group={selectedInputType}
            value={typeVal}  
          >
          <span>{ind}. {typeName}</span>
        </label>
      </div>
    {/each}
  </div>
  
  <div>
    <div>选中了多选项：{selectedMultiInputTypesList}</div>

    {#each inputTypeOpts as { typeVal, typeName }, ind}
      <div>
        <label>
          <input
            type="checkbox"
            bind:group={selectedMultiInputTypesList}
            value={typeVal}
          >
          <span>{ind}. {typeName}</span>
        </label>
      </div>
    {/each}
  </div>

  <div>
    <div>绑定原生dom</div>
    <div bind:this={origDom}>这里是原生DOM</div>
  </div>

  <div>
    <div>渲染html内容</div>
    <div>{@html htmlStr}</div>
  </div>

  <div>
    <div>样式绑定</div>
    <div style="color: {colorRed}">红字</div>
  </div>

  <div>
    <div>class绑定和条件判断</div>
    <div
      class:active={isActive}
      on:mouseover={() => isActive = true}
      on:mouseout={() => isActive = false}
      on:focus={() => {}}
      on:blur={() => {}}
    >悬停激活</div>
  </div>

  <div>
    <div>条件判断</div>
    {#if isActive}
      <div>悬停了</div>
    {:else}
      <div>离开了</div>
    {/if}
  </div>

  <div>
    <div>异步渲染与tick</div>
    <!-- <button on:click={handleTriggerPromClick}>启动异步</button> -->
    {#await prom}
      <div>加载中，{restCount}秒后显示异步消息</div>
    {:then res}
      <div>{res}</div>
    {/await}

    <div>tick前后：{tickStr}</div>
  </div>

  <div>
    <div>父子通讯</div>

    <div>1. prop</div>
    <div class="parent-block">
      <div>这是父</div>
      <HomeChildSv
        bind:this={homeChildDOM}
        strFromParent="父传子"
        on:handleParentMethod={changeByChild}
      ></HomeChildSv>

      <div>父组件中的某属性值：{strFromChild}</div>
    </div>
    
    <div>2. 父获取子组件的属性和执行子组件的方法</div>
    <button on:click={getChildProperty}>获取</button>
  </div>
</div>

<style>
  .parent-block {
    padding: 10px 15px;
    background-color: #eee;
  }
</style>
