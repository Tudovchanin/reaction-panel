<template>

  <div class="wrapper-content">
    <div class="content">
      <div class="reaction-text" v-show="!showAccessLevelPanels"> {{ reactionValue }}</div>
      <button class="btn-toggle" @click="toggleAccessLevelPanels" v-show="!showAccessLevelPanels"
        :class="{ 'animation': !activeReactions }">
        click
      </button>
      <div class="wrapper-panel" :class="{ 'hidden-panel': !showAccessLevelPanels }">
        <AccessLevelPanels :panelsData="panelsData" :colorsBtnPanels="colorsBtnPanels"
          :togglePanel="toggleAccessLevelPanels" @panel-closed="handlePanelClosed">
        </AccessLevelPanels>
      </div>

    </div>
  </div>

</template>

<script setup>
import { ref } from 'vue';
import AccessLevelPanels from './components/AccessLevelPanels.vue';

const showAccessLevelPanels = ref(false);
const activeReactions = ref(false);
const reactionValue = ref('Not reaction')

// Панели реакций
const panelsData = ref([
  { id: 1, title: 'Truthfulness', btnValue: ['off', 'low', 'medium', 'height'] },
  { id: 2, title: 'Civility', btnValue: ['off', 'low', 'medium', 'height'] },
  { id: 3, title: 'Relevance', btnValue: ['off', 'low', 'medium', 'height'] },
  { id: 4, title: 'Politeness', btnValue: ['off', 'low', 'medium', 'height'] },
]);

const colorsBtnPanels = ref([
  { offColor: '#fff', selectedColor: '#008000', unselectedColor: '#062906', filter: ['100%', '80%', '100%', '200%'] }, // Цвета для первой панели

  { offColor: '#fff', selectedColor: '#DC143C', unselectedColor: '#FFC0CB', filter: ['100%', '80%', '100%', '200%'] }, // Цвета для второй панели

  { offColor: '#fff', selectedColor: '#FFD700', unselectedColor: '		#8B4513', filter: ['100%', '60%', '70%', '150%'] }, // Цвета для третьей панели

  { offColor: '#fff', selectedColor: '#1E90FF', unselectedColor: '#7B68EE', filter: ['100%', '80%', '100%', '130%'] }, // Цвета для четвертой панели
]);


const toggleAccessLevelPanels = () => {
  showAccessLevelPanels.value = !showAccessLevelPanels.value;
};

const handlePanelClosed = (reactionStates) => {
  console.log('Панель закрыта с состоянием реакций:', reactionStates);

  const hasActiveReactions = reactionStates.some(obj => obj.status !== 'off');
  console.log(hasActiveReactions);
  if (hasActiveReactions) {
    activeReactions.value = true;
    reactionValue.value = getStringReaction(reactionStates, 'off');
  } else {
    activeReactions.value = false;
    reactionValue.value = 'Not reaction';
  }
};

function getStringReaction(arrReactionObj, valueToExclude) {
  let string = ''

  arrReactionObj.forEach(reaction => {

    if (reaction.status === valueToExclude) return;

    string += `${reaction.title.toLocaleLowerCase()} ${reaction.status}, `
  });

  return string.slice(0, - 2);

}

</script>


<style scoped>


.reaction-text {
  width: 300px;
  min-height: 100px;
  border: solid 2px #d1d1d1;
  background: linear-gradient(135deg, #f5f5f5, #857575);
  border-radius: 12px;
  padding: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  font-family: Arial, sans-serif;
  
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
  color: #333;
}

.content {
  min-height: 100vh;
  background-image: linear-gradient(45deg, #ff6b6b, #ffa500, #ffff00, #00ff00, #00ffff, #0000ff, #ff00ff);
  background-size: 200% 200%;
  animation: gradient 15s ease infinite;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 20px;
}

@keyframes gradient {
  0% {
    background-position: 0% 50%;
  }

  50% {
    background-position: 100% 50%;
  }

  100% {
    background-position: 0% 50%;
  }
}

.btn-toggle {
  background-color: black;
  color: aliceblue;
  max-width: 200px;
  width: 100%;
  min-height: 40px;
  border: solid 1px gray;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);

}

.animation {
  animation: backgroundAnimation 1s ease infinite;
}

@keyframes backgroundAnimation {
  0% {
    background-color: black;
  }

  50% {
    background-color: #333;
  }

  100% {
    background-color: black;
  }
}


/* Use calc(50% - 393px / 2) instead of transformX(-50%) because when using transformX(-50%), vertical lines appear between the buttons in the BtnReaction.vue component. */
.wrapper-panel {
  position: fixed;
  left: calc(50% - 393px / 2);
  top: 50%;
  transform: translateY(-50%);
  max-width: 393px;
  width: 100%;
  border-radius: 8px;
  background-image: linear-gradient(45deg, #211e1e, #423d33, #30301c, #101b10, #216e6e, #0b0b58, #a344a3);
  background-size: 200% 200%; /* Увеличенный размер для анимации */
  animation: gradientAnimation 10s ease infinite; /* Запуск анимации градиента */
  transition: transform .3s linear;
  overflow: hidden;
}


@keyframes gradientAnimation {
  0% {
    background-position: 0% 50%;
  }

  50% {
    background-position: 100% 50%;
  }

  100% {
    background-position: 0% 50%;
  }
}

.hidden-panel {
  position: fixed;
  transform: translateY(500%);
}


@media (max-width:750px) {

  .wrapper-panel {
    bottom: 0;
    transform: translateY(0);
    top: initial
  }

  .hidden-panel {
    position: fixed;
    transform: translateY(100%);
  }
}
</style>