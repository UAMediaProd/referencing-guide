<template>
  <div id="app">
    <div class="contain">
      <div class="col" style="background: #ddd; max-width: 20%;">
          <div style="background: #aaa;"><h2>Item type</h2></div>
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

      <div class="col" style="background: #eee; max-width: 20%;">
        <div style="background: #ccc;"><h2>Item sub-type</h2></div>
      <div v-for="(refSection, index) in level1Select.sections"
        v-bind:key="index">
        <!-- sub-heading -->
       
          
            <div
              v-for="(value, key) in refSection"
              v-bind:key="value"
              class="butts"
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
    console.log(refs);
    for (var ref in refs) {
      console.log(refs[ref]);
      var jsonObj = refs[ref].sections[0];

      console.log(refs[ref].ID);

      for (var key in jsonObj) {
        console.log("Section: " + key);
        console.log("Content: " + jsonObj[key]);
      }
    }
  },
  methods: {
    chooseLevelOne(num) {
      console.log("Column 1: " + num);
      this.level1Select = this.refList[num];
      console.log(this.level1Select);
      this.selected = num;
      this.selected2 = null;
      this.level2Select = null;
    },
    chooseLevelTwo(item) {
      this.level2Select = null;
      let that = this;
      // setTimeout to delay rendering, allowing the transition to trigger!
      setTimeout(() => {
         console.log("Column 2: " + item);
      let jsonObj = that.level1Select.sections[0];
      console.log(jsonObj);
      console.log(jsonObj[item]);
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
  color: #2c3e50;
  margin-top: 5em;
  max-width: 80%;
  margin-left: auto;
  margin-right: auto;
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
  flex-direction: column;
  flex-basis: 0;
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
  cursor: pointer;
  transition: all 0.2s ease-in-out;
  padding: 0.5em;
}

.butts:hover {
  background: #aaa;
}

.activated {
  background: black;

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
