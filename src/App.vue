<template>
  <form ref="form" class="container form">
    <h1 class="form__title">Formulario</h1>
    <transition>
      <span class="error__msg" v-if="error">Alguno de los campos esta vacio</span>
    </transition>
    <div class="form__left">
      <div class="form__section">
        <span class="input__name">Nombre Actividad*</span>
        <input type="text" class="input__field" v-model="activityName" />
      </div>
      <div class="form__section">
        <span class="input__name">Hora Inicio*</span>
        <input
          type="time"
          class="input__field"
          v-model="startTime"
          id="appt"
          name="appt"
        />
      </div>
      <div class="form__section">
        <span class="input__name">Derechos Culturales*</span>
        <select name="" v-model="selectionRight" id="" class="input__field">
          <option v-for="right in rights" :value="right.value">
            {{ right.value }}
          </option>
        </select>
      </div>
      <div class="form__section">
        <span class="input__name">Experticias*</span>
        <select name="" v-model="selectionAct" id="" class="input__field">
          <option v-for="activity in activities" :value="activity.value">
            {{ activity.value }}
          </option>
        </select>
      </div>
    </div>
    <div class="form__right">
      <div class="form__section">
        <span class="input__name">Fecha*</span>
        <input type="date" v-model="activityDate" class="input__field" />
      </div>
      <div class="form__section">
        <span class="input__name">Hora Final*</span>
        <transition>
          <span class="form__error error__msg" v-if="hourError"
            >La hora final no puede ser menor a la hora de inicio</span
          >
        </transition>
        <input class="input__field" type="time" v-model="endTime" />
      </div>
      <div class="form__section">
        <span class="input__name">Nac*</span>
        <select name="" v-model="selectionBorn" id="" class="input__field">
          <option v-for="bornPlace in born" :value="bornPlace.value">
            {{ bornPlace.value }}
          </option>
        </select>
      </div>
    </div>
    <button class="add__item" @click.prevent="addItem">Añadir</button>
  </form>
  <div class="table">
    <transition>
      <span class="clean__table" v-if="table.length === 0">SIN DATOS PARA MOSTRAR</span>
      <table v-else class="styled-table">
        <thead>
          <tr>
            <th>Consecutivo</th>
            <th>Nombre</th>
            <th>Derecho Cultural</th>
            <th>Nac</th>
            <th>Fecha</th>
            <th>Hora Inicio</th>
            <th>Final</th>
            <th>Experticia</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in table">
            <td>{{ item.consecutive }}</td>
            <td>{{ item.actName }}</td>
            <td>{{ item.selectedRight }}</td>
            <td>{{ item.selectedBorn }}</td>
            <td>{{ item.actDate }}</td>
            <td>{{ item.startHour }}</td>
            <td>{{ item.endHour }}</td>
            <td>{{ item.seletectedAct }}</td>
          </tr>
        </tbody>
      </table>
    </transition>
  </div>
</template>

<script setup>
//Ref for the
import { ref } from "vue";

//variables used
let activityName = ref(null);
let startTime = ref(null);
let activityDate = ref(null);
let endTime = ref(null);
let selectionAct = ref(null);
let selectionRight = ref(null);
let selectionBorn = ref(null);
let activities = [
  { value: "Artes plásticas" },
  { value: "Teatro" },
  { value: "Musica" },
  { value: "Danza" },
  { value: "Cocina Tradicional" },
  { value: "Juegos Tradicionales" },
  { value: "Promocion de lectura" },
];
let rights = [
  { value: "Identidad y patrimonios culturales" },
  { value: "Referencias a comunidades culturales" },
  { value: "Acceso y participación en la vida cultural" },
  { value: "Educación y formación" },
  { value: "Información y comunicación" },
  { value: "Cooperación cultural" },
];
let born = [
  { value: "ALCALÁ" },
  { value: "ANDALUCÍA" },
  { value: "ANSERMANUEVO" },
  { value: "ARGELIA" },
  { value: "BOLÍVAR" },
  { value: "BUENAVENTURA" },
  { value: "BUGA" },
];
let table = ref([]);
let prefix = ref(1);
let error = ref(false);
let hourError = ref(false);

//input validating function
function validate() {
  let inputs = document.querySelectorAll(".input__field");
  let counter = 0;
  inputs.forEach((item) => {
    if (item.value.trim() === "") counter++;
  });

  if (counter > 0) {
    error.value = true;
    return true;
  }
}

//hour converter from military to normal
function checkHour(h) {
  let hour = h.split(":");
  let theHour = hour[0];
  if (theHour > 12) {
    return (theHour % 12) + ":" + hour[1] + " PM";
  } else {
    return h + " AM";
  }
}

function addItem() {
  //restarting error messages
  error.value = false;
  hourError.value = false;
  //validating inputs
  if (validate() === true) return;
  if (startTime.value > endTime.value) {
    hourError.value = true;
    return;
  }
  //Push new entry to the table
  table.value.push({
    consecutive: "FP" + prefix.value++,
    actName: activityName.value,
    startHour: checkHour(startTime.value),
    selectedRight: selectionRight.value,
    seletectedAct: selectionAct.value,
    endHour: checkHour(endTime.value),
    actDate: activityDate.value,
    selectedBorn: selectionBorn.value,
  });
  //Clean inputs
  activityName.value = "";
  startTime.value = "";
  selectionRight.value = "";
  selectionAct.value = "";
  endTime.value = "";
  activityDate.value = "";
  selectionBorn.value = "";
}
</script>

<style lang="scss">
.form {
  display: flex;
  width: 80%;
  margin: auto;
  flex-wrap: wrap;
  justify-content: space-between;
}

.error__msg {
  width: 100%;
  text-transform: uppercase;
  margin-bottom: 20px;
  background-color: rgb(245, 193, 193);
  color: red;
  padding: 5px;
}

.form__error {
  font-size: 0.8em;
  display: block;
  margin-bottom: 5px;
  width: auto;
}

.form__title {
  width: 100%;
}

.form__left,
.form__right {
  width: 48%;
}

.form__section {
  width: 100%;
  margin-bottom: 15px;
}

.input__name {
  display: block;
  font-weight: bold;
  margin-bottom: 5px;
}

.input__field {
  width: 100%;
  border: 1px solid #a0a0a0;
  border-radius: 5px;
  height: 1.5em;
  font-size: 1.2em;

  &:focus {
    border: 1px solid transparent;
    outline: 1px solid rgb(0, 162, 255);
  }
}

.add__item {
  background-color: rgb(0, 162, 255);
  border: 0;
  padding: 10px 20px;
  color: white;
  font-family: "Roboto", sans-serif;
  text-transform: uppercase;
  letter-spacing: 1px;
  margin-left: auto;
  cursor: pointer;
  width: 10%;
  border-radius: 5px;
}

#app {
  font-family: "Roboto", sans-serif;
  box-sizing: border-box;
}

.styled-table {
  border-collapse: collapse;

  margin: 25px auto;
  font-size: 0.9em;
  font-family: sans-serif;
  min-width: 400px;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
}

.styled-table thead tr {
  background-color: rgb(0, 162, 255);

  color: #ffffff;
  text-align: left;
}

.styled-table th,
.styled-table td {
  padding: 12px 15px;
}

.styled-table tbody tr {
  border-bottom: 1px solid #dddddd;
}

.styled-table tbody tr:nth-of-type(even) {
  background-color: #f3f3f3;
}

.styled-table tbody tr:last-of-type {
  border-bottom: 2px solid rgb(0, 162, 255);
}

.styled-table tbody tr.active-row {
  font-weight: bold;
  color: rgb(0, 162, 255);
}

.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}

.clean__table {
  text-align: center;
  width: 100%;
  display: block;
  padding: 20px 0;
  font-size: 1.5em;
}
</style>
