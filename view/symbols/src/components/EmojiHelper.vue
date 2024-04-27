<template>
	<div class="w-full h-screen flex flex-col items-center justify-center overflow-hidden">
	 <Picker 
		 :data="emojiIndex" 
		 @select="showEmoji"
		 :tooltip="true"
		 :i18n="i18n"
	 />
	</div>
</template>

<script setup>
import { onBeforeMount, ref } from 'vue';
import { message } from 'ant-design-vue';
import data from "emoji-mart-vue-fast/data/all.json";
import { Picker, EmojiIndex } from "emoji-mart-vue-fast/src";
import { copyText } from '../utils/utils.js';

import "emoji-mart-vue-fast/css/emoji-mart.css";

let emojiIndex = ref(new EmojiIndex(data));
let emojisOutput = ref('');

const showEmoji = (emoji) => {
	// emojisOutput.value = emoji.native;
	// console.log('emoji: ',emoji);
	copy(emoji.native);
}

const i18n = {
  categories: {
    search: '搜索结果',
    recent: '经常使用',
    smileys: '心情',
    people: '人物',
    nature: '动物 & 大自然',
    foods: '食物 & 饮料',
    activity: '活动',
    places: '旅行 & 地标',
    objects: '物体',
    symbols: '符号',
    flags: '国旗',
    custom: '自定义',
  },
}

/**
 * 复制
 * @param {string} text
 */
const copy = (text) => {
  console.log('---- copy ----:', text);
  copyText(text).then(() => {
    message.success('复制成功');
  }).catch(err => {
    message.error('复制失败，可手动选择复制');
    console.log('---- copyText ----:', err);
  });
  if (hbuilderx) {
	  hbuilderx.postMessage({
		command: 'insert',
		data: text
	  });
  }
};

onBeforeMount(() => {
	window.addEventListener('hbuilderxReady', () => {
		if (hbuilderx) {
			hbuilderx.onDidReceiveMessage((msg)=>{
				console.log('msg: ', msg);
				if(msg.type == 'DialogButtonEvent'){
					let button = msg.button;
					if(button == '关闭'){
						hbuilderx.postMessage({
							command: 'closed'
						});
					};
				};
			});
		}
	})
})
</script>

<style scoped lang="scss">
.symbols-list{
  list-style-type: none;
  display: flex;
  flex-wrap: wrap;
  li{
    width: 32px;
    height: 32px;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 5px;
    border:1px solid #ccc;
    margin: 0 5px 5px 0;
    background-color: #fff;
    a{
      color: green;
      font-size: 16px;
      text-decoration: none;
    }
  }
}
</style>