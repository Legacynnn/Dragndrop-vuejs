<template>
  <div id="app">
    <Header/>
    <div class="board">
      <div class="lane">
        <h2 class="lane-title">Backlog</h2>
        <Container group-name="trello" @drag-start="handleDragStart('backlog', $event)" @drop="handleDrop('backlog', $event)" :get-child-payload="getChildPayload">
            <Draggable v-for="card in cards.backlog" :key="card.id">
              <Card>{{card.text}}</Card>
            </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 class="lane-title">Desenvolvimento</h2>
        <Container group-name="trello" @drag-start="handleDragStart('dev', $event)" @drop="handleDrop('dev', $event)" :get-child-payload="getChildPayload">
          <Draggable  v-for="card in cards.dev" :key="card.id">
            <Card>{{card.text}}</Card>
          </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 class="lane-title">Testes</h2>
        <Container group-name="trello" @drag-start="handleDragStart('testes', $event)" @drop="handleDrop('testes', $event)" :get-child-payload="getChildPayload">
          <Draggable v-for="card in cards.testes" :key="card.id">
            <Card>{{card.text}}</Card>
          </Draggable>
        </Container>
      </div>
      <div class="lane">
        <h2 class="lane-title">Fechados</h2>
        <Container group-name="trello" @drag-start="handleDragStart('fechados', $event)" @drop="handleDrop('fechados', $event)" :get-child-payload="getChildPayload">
          <Draggable v-for="card in cards.fechados" :key="card.id">
            <Card>{{card.text}}</Card>
          </Draggable>
        </Container>
      </div>
    </div>
  </div>
</template>

<script>
import './styles/main.css'
import Header from './components/Header'
import Card from './components/Card'
import initialCards from './initialCards'
import {Container, Draggable} from 'vue-smooth-dnd'

export default {
  name: "App",
  components: {
    Header,
    Card,
    Container,
    Draggable
  },
  data: () => ({
    cards: {
      backlog: initialCards.backlog,
      dev: initialCards.dev,
      testes: initialCards.teste,
      fechados: [],
    },
    draggingCard: {
      lane: '',
      index: -1,
      cardData: {}
    }
  }),
  methods: {
    handleDragStart(lane, dragResult) {
      const {payload, isSource} = dragResult
      if(isSource) {
        this.draggingCard = {
          lane,
          index: payload.index,
          cardData: {
            ...this.cards[lane][payload.index]
          }
        }
      }
    },
    handleDrop(lane, dropResult) {
      const {removedIndex, addedIndex} = dropResult
      
      if (lane == this.draggingCard.lane && removedIndex == addedIndex) {
        return
      }
      if(removedIndex !== null) {
        this.cards[lane].splice(removedIndex, 1)
      }
      if(addedIndex !== null) {
        this.cards[lane].splice(addedIndex, 0, this.draggingCard.cardData)
      }
    },
    getChildPayload(index) {
      return {
        index,
      }
    }
  }
}
</script>

<style>
.board {
  display: flex;
  justify-content: flex-start;
  margin: 0.8rem
}
.lane {
  background: #e0e0e0;
  width: 23rem;
  height: 30rem;
  border-radius: 0.8rem;
  box-shadow: 0 .1rem .2rem 0 rgba(33, 33, 33, 0.1);
  margin: 0 0.8rem;
  padding: 0 0.7rem;
}

.lane-title {
  padding: 0.8rem 0.5rem;
  margin-bottom: 0.6rem;
}
</style>
