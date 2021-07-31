<template>
  <div>
    <v-text-field
      v-model="searchText"
      hide-details
      dense
      placeholder="Search Available Bones"
      clearable
      class="mb-4"
      height="40px"
    />
    <div class="bones">
      <v-row v-for="(bone, i) in availableBones" :key="i" align="center" dense>
        <v-col cols="8">
          <span class="text-subtitle-2">{{ bone.text }}</span>
        </v-col>
        <v-col cols="4" align="right">
          <v-btn
            small
            color="success"
            class="elevation-0"
            @click="addBone(bone)"
          >
            Add <v-icon>mdi-chevron-right</v-icon>
          </v-btn>
        </v-col>
        <v-col cols="12">
          <v-skeleton-loader :type="bone.value" boilerplate />
          <v-divider class="mt-4" />
        </v-col>
      </v-row>
    </div>
  </div>
</template>

<script>
import Bones from "@/Bones";

export default {
  name: "AvailableBones",

  props: {
    selectedBones: { type: Array, default: () => [] }
  },

  data() {
    return {
      searchText: null
    };
  },

  computed: {
    availableBones() {
      if (this.searchText) {
        return Bones.filter(x =>
          x.text.toUpperCase().includes(this.searchText.toUpperCase())
        );
      }
      return Bones;
    }
  },

  methods: {
    addBone(bone) {
      let boneCopy = Object.assign({}, bone);
      this.selectedBones.push(boneCopy);
    }
  }
};
</script>

<style lang="scss">
.bones {
  height: calc(100vh - 132px);
  overflow-y: auto;
  overflow-x: hidden;
}
</style>
