<template>
  <v-app>
    <v-card class="pa-4 available-bones">
      <available-bones :selected-bones.sync="selectedBones" />
    </v-card>

    <div class="bone-canvas pb-5">
      <v-card class="pb-1" flat>
        <div v-if="selectedBones.length">
          <div class="pa-3">
            <p class="text-body">
              Drag to reorder bones. You can toggle a few of the options, too.
              The minus and plus buttons let you increase or decrease the number
              of instances of a bone type. When you're done, just copy the tag
              below and off you go!
            </p>
            <span class="skelly-code">{{ skellyCode }}</span>
            <v-btn-toggle multiple dense class="mt-2">
              <v-btn
                v-for="(option, i) in Object.keys(skellyLoaderOptions)"
                v-model="skellyLoaderOptions[option]"
                :key="i"
              >
                <span class="text-caption">{{ option }}</span>
              </v-btn>
            </v-btn-toggle>
            <v-divider class="my-4" />
          </div>
          <div style="position: relative;" class="mb-4">
            <draggable
              v-model="selectedBones"
              animation="200"
              handle=".skelly-drag"
            >
              <transition-group>
                <v-row
                  no-gutters
                  v-for="(bone, i) in selectedBones"
                  :key="i"
                  style="position: relative; z-index: 2;"
                  class="skelly-row px-3"
                >
                  <v-col cols="4" style="line-height: 1;">
                    {{ bone.text }}
                    <br />
                    <v-icon small>mdi-code-tags</v-icon>
                    <span class="text-caption text--secondary font-italic">
                      {{ bone.value }}
                    </span>
                  </v-col>

                  <v-col cols="6" class="skelly-drag">
                    <v-skeleton-loader
                      :type="bone.value"
                      v-bind="skellyLoaderOptions"
                      class="px-4"
                      style="cursor: grab !important;"
                    />
                  </v-col>

                  <v-col cols="2" align="right">
                    <v-btn-toggle
                      active-class="nothing"
                      style="position: relative; top: 5px;"
                      dense
                    >
                      <v-btn
                        @click="updateBoneCount(i, false)"
                        small
                        icon
                        :disabled="bone.decrementDisabled"
                      >
                        <v-icon>mdi-minus</v-icon>
                      </v-btn>
                      <v-btn @click="updateBoneCount(i)" small icon>
                        <v-icon>mdi-plus</v-icon>
                      </v-btn>
                    </v-btn-toggle>

                    <v-btn
                      @click="removeBone(i)"
                      icon
                      color="error"
                      class="ml-3"
                      small
                    >
                      <v-icon small>mdi-close</v-icon>
                    </v-btn>
                  </v-col>
                </v-row>
              </transition-group>
            </draggable>
            <div
              class="fake-card"
              :style="
                `background-color: ${
                  skellyLoaderOptions.dark ? 'rgb(30, 30, 30)' : '#ffffff'
                }`
              "
            />
          </div>
        </div>

        <h6 v-else class="text-h6 pa-3">
          Add a bone from the left to start building your Vuetify skelly loader
        </h6>
      </v-card>
    </div>
  </v-app>
</template>

<script>
import { AvailableBones } from "@/components";
import draggable from "vuedraggable";

const skellyLoaderDefaults = {
  boilerplate: false,
  dark: false,
  tile: false
};

export default {
  components: { AvailableBones, draggable },

  data() {
    return {
      selectedBones: [],
      skellyLoaderOptions: {
        boilerplate: skellyLoaderDefaults.boilerplate,
        dark: skellyLoaderDefaults.dark,
        tile: skellyLoaderDefaults.tile
      }
    };
  },

  computed: {
    skellyCode() {
      let bones = this.selectedBones.map(x => x.value).join(", ");
      let options = "";

      Object.entries(this.skellyLoaderOptions).forEach(([key, value]) => {
        if (value !== skellyLoaderDefaults[key]) {
          options += ` ${key}`;
        }
      });

      return `<v-skeleton-loader type="${bones}"${options} />`;
    }
  },

  methods: {
    removeBone(index) {
      this.selectedBones.splice(index, 1);
    },

    updateBoneCount(index, increase = true) {
      const bone = this.selectedBones[index];
      let baseValue = bone.value;
      let currentCount = 1;

      if (baseValue.includes("@")) {
        baseValue = bone.value.split("@")[0];
        currentCount = bone.value.split("@")[1];
      }

      const newCount = increase
        ? parseInt(currentCount) + 1
        : parseInt(currentCount) - 1;

      if (newCount <= 1) {
        bone.value = baseValue;
        bone.decrementDisabled = true;
        return;
      }

      bone.value = `${baseValue}@${newCount}`;
      bone.decrementDisabled = false;
    }
  }
};
</script>

<style lang="scss">
html,
body,
.v-application--wrap {
  background-color: #f1f1f1;
}

.nothing::before {
  background-color: #ffffff !important;
}

.skelly-code {
  display: block;
  padding: 4px 8px;
  background-color: #333333;
  border-radius: 4px;
  color: #ffffff;
}

.available-bones {
  display: block;
  height: calc(100vh - 40px);
  width: 340px;
  position: fixed !important;
  top: 20px;
  left: 20px;
}

.bone-canvas {
  display: block;
  width: calc(100vw - 440px) !important;
  min-width: 670px;
  max-width: 940px !important;
  position: absolute !important;
  top: 20px;
  left: 400px;
  margin: auto;
}

.fake-card {
  position: absolute;
  width: calc(50% - 12px);
  height: calc(100% + 13px);
  pointer-events: none;
  border-radius: 4px;
  bottom: -6px;
  left: 17%;
  right: 0;
  margin: auto;
  z-index: 1;
  box-shadow: 0px 3px 1px -2px rgb(0 0 0 / 20%),
    0px 2px 2px 0px rgb(0 0 0 / 14%), 0px 1px 5px 0px rgb(0 0 0 / 12%);
}
</style>
