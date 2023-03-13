<template>
  <div id="app">
    <div class="b-form" v-for="(colName, i) in ['tasks', 'dates', 'statuses']" :key="i">
      <div>
        <input :placeholder="`Введите значение ${colName}`" :ref="colName" />
        <input type="button" value="Добавить" @click="$refs[colName][0].value.trim() && addToColletion(colName, {id: makeId(), value: $refs[colName][0].value})"/>
      </div>

      <div v-for="(item, idx) in $data[colName]" :key="idx">{{ `${item.id} ${item.value}` }} <span @click="removeById(colName, item.id)">X</span></div>
    </div>

    <div class="b-form">
      <div v-for="(event, i) in events" :key="i">{{ ` ${event.id} ${getValueById('dates', event.date)} ${getValueById('tasks', event.task)} ${getValueById('statuses', event.value)}` }} <span @click="removeById('events', event.id)">X</span></div>
    </div>

    <!-- table -->
    <div class="b-table" v-if="tasks.length || dates.length">
      <div class="b-table__header">
        <div class="b-table__header-cell b-table__cell b-table__cell_main">Задачи / Даты</div>
        <div class="b-table__cell b-table__header-cell" v-for="date in dates" :key="date.id">{{`${date.value}` }}</div>
      </div>
      <div class="b-table__body">
        <div class="b-table__row" v-for="task in tasks" :key="task.id">
          <div class="b-table__cell b-table__cell_main">
            <input type="text" class="input short" v-model="task.value" />
          </div>
          <div class="b-table__cell" v-for="date in dates" :key="date.id">
            <template v-if="!statuses.length"><span>Список пуст</span></template>
            <template v-else>
              <span v-if="!getEvent(date.id, task.id)" @click="addToColletion('events', {id: makeId(), task: task.id, date: date.id, value: statuses[0].id})">+</span>
              <select v-else @change="editCollection('events', getEventId(date.id, task.id), $event.srcElement.selectedOptions[0]._value)">
                <option v-for="status in statuses" :key="status.id" :value="status.id">{{ `${status.value}` }}</option>
              </select>
            </template>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { uid } from 'uid';
export default {
  name: 'App',
  data() {
    return {
      tasks   : [],
      dates   : [],
      statuses: [],
      events  : []
    }
  },
  methods: {
    addToColletion(collectionName, model) {
      if (this[collectionName])
        this[collectionName].push(model);
    },
    removeById(collectionName, id) {
      if (this[collectionName]) {
        const idx = this[collectionName].findIndex(i => i.id === id);

        ~idx && this[collectionName].splice(idx, 1);
      }
    },
    editCollection(collectionName, id, value) {
      if (this[collectionName]) {
        const item = this[collectionName].find(i => i.id === id);

        item && this.$set(item, 'value', value);
      }
    },
    getEvent(dateId, taskId) {
      return this.events.find(e => e.date === dateId && e.task === taskId);
    },
    getEventId(dateId, taskId) {
      var event = this.getEvent(dateId, taskId);

      return event ? event.id : undefined;
    },
    getValueById(collectionName, id) {
      var item = this[collectionName].find(i => i.id === id);

      return item ? item.value : '';
    },
    makeId(length) {
      return uid(length);
    }
  }
}
</script>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
.b-form {
  padding: 10px;
  border: 1px solid #ccc;
  text-align: center;
  width: 20%;
  display: inline-block;
  vertical-align: top;
  margin-right: 10px;
}
.b-table {
  margin-top: 20px;
}
.b-table__header-cell {
  padding: 10px;
  border: 1px solid #ccc;
  text-align: center;
  display: inline-block;
}

.b-table__cell {
  width: 5%;
  display: inline-block;
  padding: 10px;
  border: 1px solid #ccc;
}

.b-table__cell_main {
  width: 10%;
}
input.short {
  width: 80%;
}
</style>
