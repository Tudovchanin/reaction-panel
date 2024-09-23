<template>
  <div class="reactions">
    <h1 class="reactions__title">Who can weigh in on this post</h1>
    <p class="reactions__paragraph">Set thresholds for each reputation category to limit who can react to your post.</p>

    <div class="reactions__panel">
      <div v-for="(panel, index) in props.panelsData" :key="index" class="reactions__panel-btn">
        <h2 class="reactions__panel-title">{{ panel.title }}</h2>
        <BtnReaction :ref="el => { if (el) { reactionButtonRefs.push(el) } }" :data-id=panel.id
          :data-reaction="panel.title" :btnValue="panel.btnValue" :colors="props.colorsBtnPanels[index]"
          @button-clicked="handleClickBtnReaction" />
      </div>
    </div>
    <div class="reactions__checkbox">
      <input type="checkbox" id="default" v-model="isDefault">
      <label for="default">Set as default settings</label>
    </div>
    <div class="reactions__btn">
      <button @click="handleCancel">Cancel</button>
      <button @click="handleConfirm">Confirm</button>
    </div>
  </div>


</template>

<script setup>
import { ref } from 'vue';
import BtnReaction from './BtnReaction.vue';


const props = defineProps({
  panelsData: {
    type: Array,
    required: true,
  },

  colorsBtnPanels: {
    type: Array,
    required: true,
  },

  togglePanel: {
    type: Function,
    required: true,
  }
});

const emit = defineEmits(['panel-closed']);
const isDefault = ref(false);
const reactionButtonRefs = ref([]);

const initialReactionStates = ref([]);
const updatedReactionStates = ref([]);
const initialActiveButtons = ref({});
const updatedActiveButtons = ref({});

const initializeReaction = () => {

  props.panelsData.forEach((panel) => {
    initialReactionStates.value.push({ id: panel.id, title: panel.title, status: panel.btnValue[0] });
    updatedReactionStates.value.push({ id: panel.id, title: panel.title, status: panel.btnValue[0] });
    initialActiveButtons.value[panel.id] = 0;
    updatedActiveButtons.value[panel.id] = 0;
  });
};

initializeReaction();


function updateArrayObj(arrayObj, idToSearch,
  property, newValue) {
  arrayObj.forEach(obj => {
    if (obj.id === idToSearch) {
      obj[property] = newValue
    }
  });
}

const handleClickBtnReaction = (data) => {
  updateArrayObj(updatedReactionStates.value, data.id, 'status', data.buttonName);
  updatedActiveButtons.value[data.id] = data.activeBtnIndex;
};



const resetReaction = () => {
  if (reactionButtonRefs.value.length) {
    reactionButtonRefs.value.forEach((el, i) => {
      el.resetStylePanel(Object.values(initialActiveButtons.value)[i]);
    });
    updatedReactionStates.value = initialReactionStates.value.map(panel => ({ ...panel }));
  }
};

const updateReaction = () => {
  initialActiveButtons.value = { ...updatedActiveButtons.value };
  initialReactionStates.value = updatedReactionStates.value.map(panel => ({ ...panel }));
}

const handlePanelClose = (states) => {
  emit('panel-closed', states);
  props.togglePanel();
};

const handleCancel = () => {
  resetReaction();
  handlePanelClose(initialReactionStates.value);
};

const handleConfirm = () => {

  updateReaction();

  const payload = {
    reactions: updatedReactionStates.value,
    isDefault: isDefault.value,
  };

  // sendReactions(payload);

  handlePanelClose(updatedReactionStates.value)

};

// sending reactions to the server

// const sendReactions = async (payload) => {
//   try {
//     const response = await fetch('#', {
//       method: 'POST',
//       headers: {
//         'Content-Type': 'application/json',
//       },
//       body: JSON.stringify(payload),
//     });

//     if (!response.ok) {
//       throw new Error('Network response was not ok');
//     }
//     const data = await response.json();
//     console.log('Reactions sent successfully:', data);
//   } catch (error) {
//     console.error('Error sending reactions:', error);
//   }
// };


</script>



<style lang="scss" scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.reactions {
  width: 100%;
  padding-top: 60px;
  background-color: #09090b;

  position: relative;
  overflow: hidden;
  background-color: transparent;
  

  &::before {
    content: '';
    position: absolute;
    left: 50%;
    top: 6px;
    transform: (translateX(-50%));
    width: 48px;
    height: 4px;
    background-color: #52525B;
    border-radius: 4px;
  }

  &__title {
    font-family: SF Pro Display, sans-serif;
    font-size: 20px;
    font-weight: 400;
    line-height: 1.5;
    color: #E4E4E7;
    padding-left: 24px;
    padding-right: 24px;
    margin-bottom: 14px;
    ;
  }

  &__paragraph {
    max-width: 345px;
    width: 100%;
    padding-left: 24px;
    padding-right: 24px;
    margin-bottom: 30px;

    font-family: SF Pro Text, sans-serif;
    font-size: 14px;
    font-weight: 500;
    line-height: 1.43;
    color: #E4E4E7;
  }

  &__panel {
    display: flex;
    flex-direction: column;
    gap: 12px;
    padding-left: 24px;
    padding-right: 24px;
    margin-bottom: 14px;

    .reactions__panel-title {
      font-family: SF Pro Text, sans-serif;
      font-size: 12px;
      font-weight: 600;
      line-height: 1.33;
      color: #E4E4E7;
      text-align: center;
    }

    .reactions__panel-btn {
      display: flex;
      flex-direction: column;
      justify-content: space-around;
      background-color: #18181B;
      height: 82px;
      padding-left: 12px;
      padding-right: 12px;
      border: solid #9a9aaf1f 1px;
      border-radius: 6px;
    }
  }

  &__checkbox {
    display: flex;
    align-items: center;
    min-height: 44px;
    padding-left: 24px;
    padding-right: 24px;
    margin-bottom: 20px;

    label {
      font-family: SF Pro Text;
      font-size: 14px;
      font-weight: 500;
      line-height: 1.43;
      text-align: left;
      color: #FFFFFF;
      padding-left: 6px;
      cursor: pointer;
    }

    input[type="checkbox"] {
      appearance: none;
      width: 16px;
      height: 16px;
      background-color: #27272A;
      border: solid #9a9aaf1f 1px;
      border-radius: 4px;
      outline: none;
      cursor: pointer;
      position: relative;

      &:checked {

        &::after {

          content: "";
          position: absolute;
          width: 6px;
          height: 6px;
          left: 50%;
          top: 50%;
          transform: translate(-50%, -50%) rotate(45deg);
          border: solid white;
          border-width: 0 2px 2px 0;
        }
      }


    }
  }

  &__btn {
    display: flex;
    justify-content: space-between;
    width: 100%;
    height: 44px;

    font-family: SF Pro Text, sans-serif;
    font-size: 14px;
    font-weight: 500;
    line-height: 1.43;



    button {
      width: 50%;
      cursor: pointer;
      border: none;
    }

    button:first-child {
      background-color: transparent;
      color: #E4E4E7;
      &:hover,
      &:active {
        background-color: #101b10;
      }
    }

    button:last-child {
      background-color: #A3E635;
      color: #18181B;
      &:hover,
      &:active {
        background-color: #67cd67;
      }
    }
  }

}



</style>