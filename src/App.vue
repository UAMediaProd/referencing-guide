<template>
  <div id="app">
    <div class="adx-direction-warning" v-if="editMode">
      <h5>Warning!</h5>
      <p>
        You're currently in edit mode for this activity. Changing the content
        here and pressing 'Save Changes' will
        <em>Change the content that gets shown to users!</em>
      </p>
    </div>
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
        <div
          v-for="(refSection, index) in level1Select.sections"
          v-bind:key="index"
        >
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
          <div
            v-if="level2Select"
            class="content"
            v-bind:class="[editMode ? 'editing' : '']"
          >
            <div v-if="!editMode" class="refInfo" v-html="level2Select"></div>

            <editor
              id="editing"
              v-if="editMode"
              api-key="no-api-key"
              :inline="true"
              :init="{
                height: 500,
                menubar: true,
                plugins: [
                  'advlist autolink lists link image charmap print preview anchor',
                  'searchreplace visualblocks code fullscreen',
                  'insertdatetime media table paste code help wordcount',
                ],
                toolbar:
                  'undo redo | formatselect | bold italic backcolor | \
           alignleft aligncenter alignright alignjustify | \
           bullist numlist outdent indent | removeformat | help',
              }"
              v-model="level2Select"
            />
          </div>
        </transition>
        <button v-if="editMode" class="butts" @click="saveChanges">
          Save changes
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import REFS from "./assets/test.json";
import Editor from "@tinymce/tinymce-vue";
import axios from "axios";

export default {
  name: "App",
  components: {
    Editor,
  },
  data: function () {
    return {
      refList: [],
      level1Select: "",
      level2Select: "",
      selected: null,
      selected2: null,
      editMode: false,
      editorOptions: {},
    };
  },
  mounted: function () {
    this.refList = REFS;

    let urlParams = new URLSearchParams(window.location.search);
    if (urlParams.has("editing") && urlParams.get("editing") == "true") {
      this.editMode = true;
    } else {
      this.editMode = false;
    }

    console.log(this.editMode);
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
      }, 20);
    },

    saveChanges() {
      console.log("Save");
      // console.log(this.selected);
      // console.log(this.selected2);

      console.log("NEW CHANGES:");
      //console.log(this.level2Select);

      this.refList[this.selected].sections[0][
        this.selected2
      ] = this.level2Select;

      console.log(this.refList);

      //take the updated refList object and save that to Github

      //first lets convert the new file to push into base64

      let updatedContent = btoa(unescape(encodeURIComponent(JSON.stringify(this.refList))));

      
      console.log(updatedContent);



      axios
        .get(
          "https://api.github.com/repos/UAMediaProd/referencing-guide/contents/src/assets/test.json"
        )
        .then(function (res) {
          var fileBiz = res.data;
          console.log(fileBiz);

          axios({
            method: "put",
            url:
              "https://api.github.com/repos/UAMediaProd/referencing-guide/contents/src/assets/test.json",
            headers: {
              Authorization: "token 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",
            },
            data: {
              message: "Updated content through Editing Mode",
              content: updatedContent,
              sha: fileBiz.sha,
            },
          })
            .then(function (response) {
              console.log(response.data);
            })
            .catch(function (err) {
              console.log("ERROR", err);
            });
        });
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
  transition: all 0.25s ease-out, color 0.25s ease-out;
  font-size: 0.9rem;
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
  transition: opacity 1s;
}

.fade-enter,
.fade-leave-active,
.fade-leave-to {
  opacity: 0;
}

.refInfo {
  transition: all 1s linear;
}

div.adx-direction,
div.adx-direction-correct,
div.adx-direction-incorrect,
div.adx-direction-warning,
div.adx-direction-question,
div.adx-direction-info {
  position: relative;
  padding: 0.833rem;
  margin: 1rem 1.728rem;
  background: #f8f9f9;
}
div.adx-direction:before,
div.adx-direction-correct:before,
div.adx-direction-incorrect:before,
div.adx-direction-warning:before,
div.adx-direction-question:before,
div.adx-direction-info:before {
  position: absolute;
  top: 0;
  margin-left: -2.074rem;
  margin-right: 1.728rem;
  content: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewBox='0 0 24 24' fill='none' stroke='%23949a9e' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3E%3Ccircle cx='12' cy='12' r='10'%3E%3C/circle%3E%3C/svg%3E");
  background: #949a9e;
  padding: 0.579rem;
  padding-top: 1.44rem;
  height: calc(100% - 0.579rem - 1.44rem);
}
div.adx-direction-info {
  background: #e6eaf3;
}
div.adx-direction-info:before {
  content: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewBox='0 0 24 24' fill='none' stroke='%23fff' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3E%3Ccircle cx='12' cy='12' r='10'%3E%3C/circle%3E%3Cline x1='12' y1='16' x2='12' y2='12'%3E%3C/line%3E%3Cline x1='12' y1='8' x2='12.01' y2='8'%3E%3C/line%3E%3C/svg%3E");
  background: #052a8a;
}
div.adx-direction-question {
  background: #f1eef9;
}
div.adx-direction-question:before {
  content: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewBox='0 0 24 24' fill='none' stroke='%23fff' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3E%3Ccircle cx='12' cy='12' r='10'%3E%3C/circle%3E%3Cpath d='M9.09 9a3 3 0 0 1 5.83 1c0 2-3 3-3 3'%3E%3C/path%3E%3Cline x1='12' y1='17' x2='12.01' y2='17'%3E%3C/line%3E%3C/svg%3E");
  background: #7356c5;
}
div.adx-direction-warning {
  background: #fff6e9;
}
div.adx-direction-warning:before {
  content: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewBox='0 0 24 24' fill='none' stroke='%23fff' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3E%3Cpath d='M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z'%3E%3C/path%3E%3Cline x1='12' y1='9' x2='12' y2='13'%3E%3C/line%3E%3Cline x1='12' y1='17' x2='12.01' y2='17'%3E%3C/line%3E%3C/svg%3E");
  background: #ffa423;
}
div.adx-direction-incorrect {
  background: #fbebea;
}
div.adx-direction-incorrect:before {
  content: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewBox='0 0 24 24' fill='none' stroke='%23fff' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3E%3Ccircle cx='12' cy='12' r='10'%3E%3C/circle%3E%3Cline x1='15' y1='9' x2='9' y2='15'%3E%3C/line%3E%3Cline x1='9' y1='9' x2='15' y2='15'%3E%3C/line%3E%3C/svg%3E");
  background: #d4322c;
}
div.adx-direction-correct {
  background: #e9f5ed;
}
div.adx-direction-correct:before {
  content: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewBox='0 0 24 24' fill='none' stroke='%23fff' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3E%3Cpath d='M22 11.08V12a10 10 0 1 1-5.93-9.14'%3E%3C/path%3E%3Cpolyline points='22 4 12 14.01 9 11.01'%3E%3C/polyline%3E%3C/svg%3E");
  background: #22964f;
}
div.adx-direction > *,
div.adx-direction-info > *,
div.adx-direction-question > *,
div.adx-direction-warning > *,
div.adx-direction-incorrect > *,
div.adx-direction-correct > * {
  margin-left: 1.44rem;
}

.editing {
  border: 1px solid #ededed;
}
</style>
