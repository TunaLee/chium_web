<template>
  <div class="bg-white notosanskr-regular">
    <div class="q-px-md">
      <div class="ask-picture q-pa-none">
        <span>공간사진</span>
        을 보여주세요.
      </div>
      <div style="color:#767676; font-szie:14px letter-spacing: -0.7px;" class="q-mt-xs">(선택 사항)</div>
      <div class="select-photo">
        <div
          class="display-selected-photo row justify-between items-center"
          style="height: 58px;"
          v-for="(pic, index) in selectedImages"
          :key="index"
        >
          <div class="row items-center">
            <q-icon class="text-dark" size="24px" style="margin-right: 10px;">
              <img src="~assets/img_icon.svg" />
            </q-icon>
            <div style="width: 232px;" class="ellipsis">{{ pic }}</div>
          </div>
          <q-btn
            flat
            style="color: #151515;border: solid 1px #DEDEDE"
            @click="removeSelectedImage(pic)"
            label="삭제"
          />
        </div>
        <q-btn
          @click="open('bottom')"
          flat
          class="upload-button full-width"
          :class="{'item-above': selectedImages.length}"
          label="공간 사진 업로드"
          v-if="selectedImages.length < 3"
        />
        <div
          class="upload-to-text"
          :class="{'uploaded-three': selectedImages.length > 2}"
        >최대 3장까지 사진을 업로드 할 수 있습니다.</div>
      </div>
      <q-dialog v-model="dialog" :position="position">
        <q-card style="height: 541px;" :style="widthMax" class="popup-content">
          <q-layout view="Lhh lpR fff" container class="bg-white">
            <q-header class="bg-white">
              <q-toolbar class="row justify-between">
                <div class="popup-header text-dark notosanskr-regular">사진선택</div>
                <q-btn flat color="grey" icon="clear" @click="dialog=false" />
              </q-toolbar>
            </q-header>
            <q-footer class="bg-secondary text-white q-pa-none">
              <q-toolbar inset class="q-pa-none">
                <q-btn
                  class="text-center text-white full-width"
                  style="height: 60px"
                  @click="dialog=false"
                >
                  <div>선택완료</div>
                </q-btn>
              </q-toolbar>
            </q-footer>
            <q-page-container style="height: 490px;">
              <q-page padding class="q-pt-none">
                <div class="row">
                  <div class="col-4 bg-grey-14" v-for="(img, index) in spaceImages" :key="index">
                    <div class="img-space" @click="selectImage(img)">
                      <q-img
                        :src="img.url"
                        spinner-color="white"
                        style="height: 100%; max-width: 100%"
                        :ratio="1"
                      />
                      <div class="circle-selected" :class="{'selected': isImageSelected(img)}">
                        <span v-if="isImageSelected(img)">1</span>
                      </div>
                    </div>
                  </div>
                </div>
              </q-page>
            </q-page-container>
          </q-layout>
        </q-card>
      </q-dialog>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      dialog: false,
      position: "top",
      selectedImages: [],
    };
  },
  computed: {
    widthMax() {
      return { width: window.innerWidth + "px", "max-width": "1000px" };
    },
    spaceImages() {
      const dummyImage = [];
      for (let item = 0; item < 12; item++) {
        dummyImage.push({
          id: item,
          name: `photo_${item}_2020`,
          url: "https://placeimg.com/500/300/nature?t=" + Math.random(),
        });
      }
      return dummyImage;
    },
  },
  methods: {
    open(position) {
      this.position = position;
      this.dialog = true;
    },
    removeSelectedImage(imageName) {
      this.selectedImages = this.selectedImages.filter((i) => i != imageName);
    },
    selectImage(image) {
      if (this.selectedImages.length < 3) {
        this.selectedImages.push(image.name);
      } else if (this.selectedImages.includes(image.name)) {
        this.removeSelectedImage(image.name);
      }
    },
    isImageSelected(image) {
      return this.selectedImages.includes(image.name);
    },
  },
  created() {
    this.$emit("next", true);
  },
};
</script>

<style lang="scss" scoped>
.ask-picture {
  width: 129px;
  font-size: 27px;
  line-height: 40px;
  /* or 148% */

  font-style: normal;
  font-weight: normal;
  font-size: 27px;
  line-height: 40px;
  /* or 148% */
  margin-top: 30px;
  letter-spacing: -1.35px;
  span {
    color: #46b3fc;
  }
}

.select-photo {
  margin-top: 35px;
  .display-selected-photo {
    height: 58px;
    border-bottom: 1px solid #0000001a;
  }
  .upload-button {
    height: 50px;
    color: #151515;
    background: #e8eaeb;
    border: 1px solid #dedede;
    &.item-above {
      margin-top: 20px;
    }
  }
  .upload-to-text {
    margin-top: 30px;
    font-size: 14px;
    line-height: 21px;
    /* identical to box height, or 150% */
    color: #c0c0c0;
    letter-spacing: -1.35px;
    &.uploaded-three {
      color: #46b3fc;
    }
  }
}

.popup-content {
  border-top-left-radius: 20px;
  border-top-right-radius: 20px;
  .popup-header {
    font-weight: 500;
    font-size: 15px;
    line-height: 26px;
    /* identical to box height, or 173% */
    letter-spacing: -0.85px;
  }
  .img-space {
    width: 100%;
    // padding-top: 75%; /* 4:3 Aspect Ratio */
    border: #fff solid 0.5px;
    position: relative;
    cursor: pointer;
    .circle-selected {
      position: absolute;
      z-index: 1000;
      bottom: 0;
      right: 0;
      width: 24px;
      height: 24px;
      background: transparent;
      border: solid 2px white;
      border-radius: 50%;
      margin-bottom: 10px;
      margin-right: 10px;
      display: flex;
      justify-content: center;
      align-items: center;
      color: white;

      &.selected {
        background: #46b3fc;
      }
    }
  }
}
</style>