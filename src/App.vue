<template>
  <div id="app">
    <my-header />
    <div class="board">
      <div class="lane">
        <h2 class="lane-title">ON-HOLD</h2>
        <Container
          group-name="trello"
          @drag-start="handleDragStart('hold', $event)"
          @drop="handleDrop('hold', $event)"
          :getChildPayload="getChildPayload"
          :drop-placeholder="{ class: 'placeholder' }"
        >
          <Draggable v-for="card in cards.hold" :key="card.id">
            <my-card>
              {{ card.text }}
            </my-card>
          </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 class="lane-title">IN-PROGRESS</h2>
        <Container
          group-name="trello"
          @drag-start="handleDragStart('progress', $event)"
          @drop="handleDrop('progress', $event)"
          :getChildPayload="getChildPayload"
          :drop-placeholder="{ className: 'placeholder' }"
        >
          <Draggable v-for="card in cards.progress" :key="card.id">
            <my-card>
              {{ card.text }}
            </my-card>
          </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 class="lane-title">NEEDS-REVIEW</h2>
        <Container
          group-name="trello"
          @drag-start="handleDragStart('review', $event)"
          @drop="handleDrop('review', $event)"
          :getChildPayload="getChildPayload"
          :drop-placeholder="{ className: 'placeholder' }"
        >
          <Draggable v-for="card in cards.review" :key="card.id">
            <my-card>
              {{ card.text }}
            </my-card>
          </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 class="lane-title">APPROVED</h2>
        <Container
          group-name="trello"
          @drag-start="handleDragStart('approved', $event)"
          @drop="handleDrop('approved', $event)"
          :getChildPayload="getChildPayload"
          :drop-placeholder="{ className: 'placeholder' }"
        >
          <Draggable v-for="card in cards.approved" :key="card.id">
            <my-card>
              {{ card.text }}
            </my-card>
          </Draggable>
        </Container>
      </div>
    </div>
  </div>
</template>

<script>
import MyHeader from "./components/MyHeader.vue";
import MyCard from "./components/MyCard.vue";
import initialCards from "@/initialCards";
import { Container, Draggable } from "vue3-smooth-dnd";
export default {
  name: "App",
  components: {
    MyHeader,
    MyCard,
    Container,
    Draggable,
  },
  data: () => ({
    cards: {
      hold: initialCards.hold,
      progress: initialCards.progress,
      review: initialCards.review,
      approved: [],
    },
    draggingCard: {
      lane: "",
      index: -1,
      cardData: {},
    },
  }),
  methods: {
    handleDragStart(lane, dragResult) {
      const { payload, isSource } = dragResult;
      if (isSource) {
        this.draggingCard = {
          lane,
          index: payload.index,
          cardData: {
            ...this.cards[lane][payload.index],
          },
        };
      }
    },
    handleDrop(lane, dropResult) {
      const { removedIndex, addedIndex } = dropResult;
      if (lane === this.draggingCard.lane && removedIndex === addedIndex) {
        return;
      }
      if (removedIndex !== null) {
        this.cards[lane].splice(removedIndex, 1);
      }
      if (addedIndex !== null) {
        this.cards[lane].splice(addedIndex, 0, this.draggingCard.cardData);
      }
    },
    getChildPayload(index) {
      return {
        index,
      };
    },
  },
};
</script>

<style>
*,
*::before,
*::after {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
  --main-bg-color: #cfe0e8;
  --main-accent-color: #bccad6;
  --main-white-color: #f0f0f0;
  --main-gray-color: #e0e2e4;
}
html {
  font-size: 62.5%;
}
body {
  background-color: var(--main-bg-color);
  margin: 0;
  padding: 0;
}
.board {
  display: flex;
  justify-content: flex-start;
  margin: 1.2rem 0.8rem;
  align-items: flex-start;
}
.lane {
  background: var(--main-gray-color);
  width: 23rem;
  border-radius: 0.8rem;
  box-shadow: 0 0.1rem 2rem 0 rgba(33, 33, 33, 0.1);
  margin: 0 0.8rem;
  padding: 0 0.7rem;
}
.lane-title {
  padding: 0.8rem 0.5rem;
  margin-bottom: 0.6rem;
}
.placeholder {
  background: rgba(33, 33, 33, 0.08);
  border-radius: 0.4rem;
  transform: scaleY(0.85);
  transform-origin: 0% 0%;
}
</style>
