<template>
  <div id="app">
    <div class="contain">
      <div class="col">
          <div><h2>Item type</h2></div>
          <!-- main headings -->
          <div
            v-for="(ref, ind) in refList"
            v-bind:key="ref.ID"
            class="butts"
            @click="chooseLevelOne(ind)"
            :class="[selected == ind ? 'activated' : '']"
          >
            {{ ref.ID }}
          </div>
        
      </div>

      <div class="col">
        <div><h2>Item sub-type</h2></div>
      <div v-for="(refSection, index) in level1Select.sections"
        v-bind:key="index">
        <!-- sub-heading -->
       
          
            <div
              v-for="(value, key) in refSection"
              v-bind:key="value"
              class="butts col2"
              @click="chooseLevelTwo(key)"
              :class="[selected2 == key ? 'activated' : '']"
            >
              {{ key }}
            </div>
         
      
      </div>

        <!-- show things -->
      </div>

      <div class="col">
        <transition name="fade">
        <div v-if="level2Select" class="content">
          <div class="refInfo" v-html="level2Select"></div>
        </div>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
import REFS from "./assets/refs.json";

export default {
  name: "App",
  data: function () {
    return {
      refList: [],
      level1Select: "",
      level2Select: "",
      selected: null,
      selected2: null,
    };
  },
  mounted: function () {
    this.refList = REFS;
    let refs = this.refList;

    for (var ref in refs) {

      var jsonObj = refs[ref].sections[0];

    }
  },
  methods: {
    chooseLevelOne(num) {

      this.level1Select = this.refList[num];
     
      this.selected = num;
      this.selected2 = null;
      this.level2Select = null;
    },
    chooseLevelTwo(item) {
      this.level2Select = null;
      let that = this;
      // setTimeout to delay rendering, allowing the transition to trigger!
      setTimeout(() => {
   
      let jsonObj = that.level1Select.sections[0];

      that.level2Select = jsonObj[item];
      that.selected2 = item;
      },20);
     
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Open+Sans:ital,wght@0,400;0,600;0,700;1,400;1,700&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Crimson+Text:ital@0;1&display=swap");

#app {
  font-family: "Open Sans", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #303030;
  margin-top: 5em;
  max-width: 80%;
  margin-left: auto;
  margin-right: auto;
  font-size: 1em;
  line-height: 1.7;
}

.contain {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  width: 100%;
  margin: 1em;
  height: 100vh;
}

.col {
  display: flex;
  flex-wrap: wrap;
  flex-direction: column;
  flex-basis: 20%;
  flex-grow: 1;
  /* background: black; */
}


.example {
  background: #efefef;
  padding: 1em 1em 1em 2em;
  border-radius: 5px;
  line-height: 1.5;
  font-family: "Crimson Text", serif;
  font-size: 1.2em;
}

.textExample {
  background: #efefef;
  font-family: "Crimson Text", serif;
  font-size: 1.2em;
  padding: 0.1em;
  border-radius: 5px;
}

.butts {
    vertical-align: middle;
    margin: 0 0 1rem 0;
    font-family: inherit;
    padding: 1em 1.5em;
    border: 1px solid transparent;
    border-radius: 0;
    transition: all .25s ease-out,color .25s ease-out;
    font-size: .9rem;
    line-height: 1;
    text-align: center;
    cursor: pointer;
    background-color: #005a9c;
    color: #fff;
    text-decoration: none;
    text-transform: uppercase;
    max-width: 80%;
  
}

.col2 {
  border: 1px solid #000;
    color: #000;
    background: none;
}

.butts:hover {
  background: #004d85;
}

.col2:hover {
  border-color: #8a8a8a;
  background: none;
  color: #000;
}

.activated {
  background: #102535;
  color: white;
}

.activated:hover {
  background: #102535;
  color: white;
}

.content {
  padding: 1em;
}

.fade-enter-active {
  transition: opacity 1s
}

.fade-enter,
.fade-leave-active, .fade-leave-to {
  opacity: 0
}

.refInfo {
  transition: all 1s linear;
}


</style>
