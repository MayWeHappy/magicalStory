<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from "vue";
import { defineEmits } from "vue";

const emit = defineEmits(["select"]);

const scenes = [
  {
    name: "The Enchanted Cave",
    description: "Echoes of forgotten tales and glowing crystals fill the air.",
    goal: "Defeat the Crystal Guardian to unlock the treasure chest.",
    image: "/assets/cave.png",
  },
  {
    name: "The Ancient Castle",
    description: "Portraits hum songs, and staircases twist by magic.",
    goal: "Battle the Phantom Knight to claim the enchanted key.",
    image: "/assets/castle.png",
  },
];

// 用于存储选中的场景名称
const selectedScene = ref<string | null>(null);
const focusedIndex = ref<number>(0); // 添加 focusedIndex 变量

// 处理场景选择
const selectScene = (name: string, description: string, goal: string) => {
  if (selectedScene.value) return; // 如果已经选择场景，阻止重复选择
  selectedScene.value = name; // 记录选中的场景
  const scene = `${name}: ${description} Goal: ${goal}`;
  emit("select", scene);
};

// 处理键盘导航
const handleKeydown = (event: KeyboardEvent) => {
  if (event.key === "ArrowDown") {
    focusedIndex.value = (focusedIndex.value + 1) % scenes.length;
  } else if (event.key === "ArrowUp") {
    focusedIndex.value = (focusedIndex.value - 1 + scenes.length) % scenes.length;
  } else if (event.key === "Enter") {
    const scene = scenes[focusedIndex.value];
    selectScene(scene.name, scene.description, scene.goal);
  }
};

onMounted(() => {
  window.addEventListener("keydown", handleKeydown); // 添加键盘事件监听器
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", handleKeydown); // 移除键盘事件监听器
});
</script>

<template>
  <div class="scene-selection">
    <h2 class="selection-title">Choose Your Scene</h2>
    <ul class="scene-list">
      <li v-for="(scene, index) in scenes" :key="scene.name" class="scene-item">
        <button
          :disabled="Boolean(selectedScene) && selectedScene !== scene.name"
          @click="selectScene(scene.name, scene.description, scene.goal)"
          class="scene-button"
          :class="{
            disabled: Boolean(selectedScene) && selectedScene !== scene.name,
            focused: focusedIndex === index // 动态添加 focused 类
          }"
          tabindex="0"
        >
          <!-- 场景图片 -->
          <img :src="scene.image" :alt="scene.name" class="scene-image" />
          <!-- 场景文字信息 -->
          <div class="scene-info">
            <div class="scene-name">{{ scene.name }}</div>
            <div class="scene-description">{{ scene.description }}</div>
            <div class="scene-goal">{{ scene.goal }}</div>
          </div>
        </button>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.scene-selection {
  text-align: left;
  padding: 1.5rem;
  background: rgba(42, 30, 92, 0.9); /* 深紫色背景 */
  border-left: 4px solid #ffa500; /* 金色边框 */
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3); /* 柔和阴影 */
  color: #ffffff; /* 白色文字 */
  font-family: "Roboto", Arial, sans-serif; /* 无衬线字体 */
}

/* 标题样式 */
.selection-title {
  font-size: 1.8rem;
  color: #ffa500;
  font-weight: bold;
  margin-bottom: 1rem;
  text-shadow: 0 2px 5px rgba(0, 0, 0, 0.6);
  text-align: center;
}

/* 列表样式 */
.scene-list {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.scene-item {
  margin-bottom: 1.2rem; /* 列表项之间的间距 */
}

/* 按钮样式 */
.scene-button {
  display: flex; /* 让图片和文字水平排列 */
  align-items: center; /* 垂直居中 */
  width: 100%;
  text-align: left;
  padding: 1rem 1.5rem;
  background: linear-gradient(135deg, #2a1e5c, #4e342e); /* 深紫到深棕渐变 */
  border: 2px solid #ffa500;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.3s ease-in-out;
  color: #ffffff;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

/* 禁用样式 */
.scene-button:disabled {
  cursor: not-allowed;
  opacity: 0.6;
  background: #4e342e;
}

/* 鼠标悬停时 */
.scene-button:hover:enabled,
.scene-button.focused { /* 添加 focused 类的样式 */
  background: linear-gradient(135deg, #4e342e, #2a1e5c); /* 反转渐变 */
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.5);
  transform: scale(1.03);
}

/* 场景图片 */
.scene-image {
  width: 60px; /* 图片大小 */
  height: 60px;
  object-fit: contain; /* 确保图片完整显示 */
  transition: transform 0.3s;
}

/* 鼠标悬停时 */
.scene-button:hover .scene-image,
.scene-button.focused .scene-image { /* 添加 focused 类的样式 */
  transform: scale(1.1); /* 放大效果 */
}

/* 场景信息容器 */
.scene-info {
  display: flex;
  flex-direction: column; /* 垂直排列名字和描述 */
  justify-content: center;
  margin-left: 1rem; /* 与图片间的间距 */
}

/* 名字样式 */
.scene-name {
  font-size: 1.4rem;
  color: #ffa500;
  font-weight: bold;
  margin-bottom: 0.5rem;
}

/* 描述样式 */
.scene-description {
  font-size: 1.2rem;
  color: #ffffff;
  line-height: 1.4;
}

/* 目标样式 */
.scene-goal {
  font-size: 1rem;
  color: #ffffff;
  line-height: 1.4;
}
</style>