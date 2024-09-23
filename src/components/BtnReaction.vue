<template>
  <div class="slider-btn" :style="setColorVariables" @mouseover="handleMouseOverSlider($event)"
    @mouseleave="handleMouseLeaveSlider($event)">
    <button class="react-btn" :class="{ 'active-btn': index === activeBtn }" v-for="(btn, index) in props.btnValue"
      :key="index" @click="handleClickBtn(index)" data-value="btn">
      <span @click="handleClickDecor" @mouseenter="handleMouseEnterDecor($event, index)" :style="{
        backgroundColor: getColorDecor(index),
        filter: `saturate(${getFilterStyle(index)})  brightness(${getFilterStyle(index)})`
      }" :class="getButtonClasses(index)">
      </span>
      <span class="react-btn__label">{{ btn }}</span>
    </button>
  </div>
</template>

<script setup>
import { ref, useAttrs, defineExpose } from 'vue';

const attrs = useAttrs();

const props = defineProps({
  btnValue: {
    type: Object,
    required: true,
  },
  colors: {
    type: Object,
    required: true,
  },
});


const setColorVariables = {
  '--shadow-color-v1': props.colors.selectedColor,
  '--shadow-color-v2': props.colors.offColor,
};

const emit = defineEmits(['button-clicked']);

const activeBtn = ref(0);
const hiddenIcon = ref(false);
const clicked = ref(false);
const hoverSliderDecorBtn = ref(false);
const hoverIndexBtn = ref(null);

const handleClickBtn = (index) => {
  hiddenIcon.value = false;
  clicked.value = true;
  activeBtn.value = index;


  emit('button-clicked', {
    id: attrs['data-id'],
    buttonName: props.btnValue[index],
    panelTitle: attrs['data-reaction'],
    activeBtnIndex: activeBtn.value
  });
};
// const setColorVariables = () => {

//   return {

//     '--shadow-color-v1': props.colors.selectedColor,
//     '--shadow-color-v2': props.colors.offColor,
//   }
// };

const getColorDecor = (index) => {
  if (activeBtn.value && index !== 0 && !hiddenIcon.value) {
    return index <= activeBtn.value ? props.colors.selectedColor : props.colors.unselectedColor;
  }
  if (hiddenIcon.value && index === hoverIndexBtn.value) {
    return index !== 0 ? props.colors.selectedColor : props.colors.offColor
  }
};

const getFilterStyle = (index) => {

  if (!hiddenIcon.value && index <= activeBtn.value) {

    return props.colors.filter[activeBtn.value];
  }
  else if (hiddenIcon.value && hoverIndexBtn.value === index) {

    return props.colors.filter[index];
  }
  else {
    return '100%';
  }
};

const getButtonClasses = (index) => {
  return {
    'hidden-icon': hiddenIcon.value,
    'active-icon': index === activeBtn.value,
    'react-btn__decoration': true,
  };
};

const handleMouseEnterDecor = (e, index) => {

  hoverIndexBtn.value = index;

  if (e.target.classList.contains('active-icon')) return;

  hoverSliderDecorBtn.value = true;

  if (clicked.value) {
    clicked.value = false;
  }
  if (hoverSliderDecorBtn.value) {
    hiddenIcon.value = true;
  }
};

const handleMouseOverSlider = (e) => {
  const target = e.target;
  if (target.classList.contains('react-btn__decoration')) return;
  hiddenIcon.value = false;
  hoverSliderDecorBtn.value = false;
};

const handleMouseLeaveSlider = (e) => {
  const target = e.target;
  if (target.classList.contains('react-btn__decoration')) return;
  hiddenIcon.value = false;
  hoverSliderDecorBtn.value = false;
};

const handleClickDecor = (index) => {
  activeBtn.value = index;
};

const resetStylePanel = (index) => {
  activeBtn.value = index;
};

defineExpose({
  resetStylePanel
});
</script>

<style scoped>
.slider-btn {
  display: flex;
  width: 100%;
}

.react-btn {
  display: grid;
  width: 100%;
  height: 38px;
  cursor: pointer;
  border: none;
  color: aliceblue;
  background-color: transparent;
}


.react-btn__label {
  font-family: SF Pro Text, sans-serif;
  font-size: 12px;
  line-height: 1.33;
}

.react-btn__decoration {
  height: 10px;
  width: 100%;
  background-color: #FFFFFF40;
  transition: background-color .3s ease;
  position: relative;
}

.slider-btn button:last-child .react-btn__decoration {
  border-radius: 0 20px 20px 0;
}

.slider-btn button:first-child .react-btn__decoration {
  border-radius: 20px;
}

.slider-btn button:nth-child(2) .react-btn__decoration {
  border-radius: 20px 0 0 20px;
}

.react-btn__decoration::before {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 10px;
  height: 10px;
  background-color: transparent;
  border: solid 4px #FFFFFF;
  border-radius: 100%;
  opacity: 0;

  box-shadow: 0 0 10px rgba(255, 255, 255, 0.4),
    0 0 20px rgba(255, 255, 255, 0.4);
}

.react-btn__decoration.active-icon:not(.hidden-icon):before {
  opacity: 1;
}

.react-btn:nth-child(2) .hidden-icon:hover,
.react-btn:nth-child(3) .hidden-icon:hover,
.react-btn:nth-child(4) .hidden-icon:hover {

  box-shadow:
    0 -2px 10px var(--shadow-color-v1),
    /* Свечение вверх */
    0 2px 10px var(--shadow-color-v1);
  /* Свечение вниз */
}

.react-btn:nth-child(1) .hidden-icon:hover {
  box-shadow:
    0 -2px 10px var(--shadow-color-v2),
    0 2px 10px var(--shadow-color-v2);
}

.active-btn {
  cursor: initial;
  /* Убираем курсор для родительского элемента, если он активен */
}

.active-btn .active-icon.hidden-icon {
  cursor: pointer;
}
</style>
