<template>
  <div class="bg-white">
    <q-layout view="hHh lpR fFf" class="bg-white">
      <!-- Header Starts here -->
      <q-header class="text-dark row justify-center bg-white" height-hint="98">
        <div :style="widthMax" class="bg-white">
          <nav-header-info name="견적신청" :backTo="returnTo" closedTo="store"></nav-header-info>
          <div class="q-px-sm">
            <process-indicator
              :totalSteps="totalSteps"
              :activeStep="activeStep"
              :activeColor="activeColor"
            ></process-indicator>
          </div>
        </div>
      </q-header>
      <!-- Child Routes content here            -->
      <q-page-container class="row justify-center bg-white" style="min-height:100vh;">
        <router-view
          @next="setBtnReady"
          class="q-ma-none q-pa-none bg-white"
          style="background: #F2F2F2"
          :style="widthMax"
          @onHandleStep="_ => activeStep = _"
        />
      </q-page-container>
      <!-- Request Footer content here -->
      <q-footer class="row justify-center bg-white" v-if="!isBtnHidden">
        <q-toolbar class="bg-white text-white q-py-sm q-px-none" :style="widthMax">
          <q-btn
            class="full-width notosanskr-regular btn-footer"
            :class="{'btn-ready' : isBtnReady }"
            :disable="!isBtnReady"
            style="font-size:17px;"
            :rounded="false"
            flat
            :label="btnLabel"
            @click="myBtnFunction"
          />
        </q-toolbar>
      </q-footer>
    </q-layout>
  </div>
</template>

<script>
import NavHeaderInfo from "components/Utility/NavHeaderInfo";
import ProcessIndicator from "components/Utility/ProcessIndicator";
import { toInteger } from 'lodash';

export default {
  name: "RequestEstimate",
  components: {
    "process-indicator": ProcessIndicator,
    "nav-header-info": NavHeaderInfo,
  },
  data() {
    return {
      totalSteps: 6,
      activeStep: 1,
      activeColor: "",
      btnLabel: "다음",
      isBtnReady: false,
      isBtnHidden: false,
      redirectTo: "",
      returnTo: "",
      requestComplete: false,
    };
  },
  computed: {
    widthMax() {
      return { width: window.innerWidth + "px", "max-width": "1000px" };
    },
    stepsList() {
      return [
        {
          routeName: "space_type",
          indicatorColor: "#55E2EB",
          stepNum: 1,
          btnReady: false,
          redirect: "floor_space",
          returnTo: "",
        },
        {
          routeName: "floor_space",
          indicatorColor: "#46B3FC",
          stepNum: 2,
          btnReady: false,
          redirect: "work_type",
          returnTo: "space_type",
        },
        {
          routeName: "work_type",
          indicatorColor: "#3A81EC",
          stepNum: 3,
          btnReady: false,
          redirect: "due_date",
          returnTo: "floor_space",
        },
        {
          routeName: "due_date",
          indicatorColor: "#495AEB",
          stepNum: 4,
          btnReady: false,
          redirect: "picture_space",
          returnTo: "work_type",
          hideBtnFooter: true,
        },
        {
          routeName: "picture_space",
          indicatorColor: "#754BEB",
          stepNum: 5,
          btnReady: true,
          redirect: "complete_estimate",
          returnTo: "due_date",
        },
        {
          routeName: "complete_estimate",
          indicatorColor: "#2F00BE",
          stepNum: 6,
          btnReady: true,
          redirect: "store",
          returnTo: "picture_space",
        },
      ];
    },
  },
  methods: {
    setBtnReady(val) {
      this.isBtnReady = val;
      if (val) {
        this.isBtnHidden = false;
      } else {
        this.setProcessIndicator();
      }
    },
    setProcessIndicator() {
      const found_route = this.stepsList.find(
        (step) => step.routeName == this.$route.name
      );
      if (found_route) {
        this.isBtnReady = found_route.btnReady;
        this.activeStep = found_route.stepNum;
        this.activeColor = found_route.indicatorColor;
        this.returnTo = found_route.returnTo;
        if (found_route.redirect) {
          this.redirectTo = found_route.redirect;
          this.requestComplete = false;
        } else {
          this.redirectTo = "";
          this.requestComplete = true;
        }
        this.isBtnHidden = found_route.hideBtnFooter ? true : false;
      }
    },
    myBtnFunction() {
      const { params: { step } } = this.$route;
      this.$router.push(`/request/${toInteger(step)+1}`);
    },
  },
  watch: {
    $route(to, from) {
      this.isBtnReady = false;
      this.setProcessIndicator();
    },
    '$route.params.step': {
      deep: true,
      handler(newStep) {
        this.activeStep = toInteger(newStep);
      }
    }
  },
  created() {
    this.setProcessIndicator();
  },
};
</script>

<style lang="scss">
.btn-footer {
  color: #919698;
  background: #e8eaeb;
  border-radius: 0;
  height: 60px;
}

.btn-ready {
  color: #ffffff;
  background: #46b3fc;
}
</style>
