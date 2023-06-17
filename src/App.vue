<template>
  <div id="app">
    <div
      class="bg-success bg-gradient sticky-top p-2 d-flex justify-content-between align-items-center justify-content-lg-around mb-3"
    >
      <h5 class="sitename mx-2 fw-bolder">{{ sitename }}</h5>

      <div class="checkoutBox">
        <button
          class="btn-warning btn bg-gradient position-relative mx-2"
          v-if="cartCount() > 0 || !showLesson"
          type="button"
          @click="switchPage"
        >
          <i class="bi bi-cart-check-fill me-1"></i
          >{{ showLesson ? "Checkout" : "Activities" }}
          <span
            v-if="showLesson"
            class="badge bg-danger position-absolute top-0 start-100 translate-middle round-pill"
            >{{ cartCount() }}</span
          >
        </button>
      </div>
    </div>

    <lessons-component
      v-if="showLesson"
      @handle_lessons="handle_lessons"
    ></lessons-component>

    <!-- alerts box  -->
    <div class="position-fixed bottom-0 start-0 p-3" style="z-index: 11">
      <div
        id="liveToast"
        class="toast hide"
        role="alert"
        aria-live="assertive"
        aria-atomic="true"
        ref="toast"
      >
        <div class="toast-header">
          <i :class="notifyMsg.classAttr"></i>
          <strong class="me-auto">{{ notifyMsg.header }}</strong>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="toast"
            aria-label="Close"
          ></button>
        </div>
        <div class="toast-body">
          <p><strong>Subject: </strong>{{ notifyMsg.subject }}</p>
          <p><strong>Location: </strong>{{ notifyMsg.location }}</p>
          <p><strong>Price: </strong>{{ notifyMsg.price | formatPrice }}</p>
          <p><strong>Spaces: </strong>{{ notifyMsg.space }}</p>
        </div>
      </div>
    </div>

    <!-- cirlces box  -->
    <div class="position-relative">
      <div class="circle1"></div>
      <div class="circle2"></div>
    </div>

    <div class="shapes">
      <div class="shape1"></div>
      <div class="shape2"></div>
    </div>
  </div>
</template>

<script>
import lessonsComponent from "./components/LessonsComponent.vue";
let lesson_url = "http://localhost:3000/collection/lessons";
export default {
  name: "App",
  components: { lessonsComponent },
  data() {
    return {
      sitename: "After School Activities",
      lessons: [],
      cart: {},
      notifyMsg: {
        header: "",
        type: "",
        classAttr: "",
        subject: "",
        location: "",
        price: "",
        space: "",
      },
      showLesson: true,
    };
  },
  methods: {
    switchPage() {
      fetch(lesson_url)
        .then((response) => response.json())
        .then((json) => {
          this.lessons = json;
        });
      this.showLesson = !this.showLesson; //flips the showLesson value
    },
    cartCount() {
      return Object.keys(this.cart).length;
    },
    handle_lessons(searchTerm) {
      if (searchTerm) {
        let search_url = lesson_url + "/" + searchTerm;

        fetch(search_url)
          .then((resp) => resp.json())
          .then((json) => {
            // console.log("results from server ", json);
            this.lessons = json;
          })
          .catch((e) => {
            console.error(e);
          });
      } else {
        fetch(lesson_url)
          .then((response) => response.json())
          .then((json) => {
            this.lessons = json;
          });
      }
    },
  },
  created: () => {
    fetch(lesson_url)
      .then((response) => response.json())
      .then((json) => {
        App.lessons = json;
      });
  },
};
</script>

<style>
* {
  font-family: arial;
}

.sitename {
  color: ivory;
}
.controlBox {
  border-radius: 7px;
}
.toast-body {
  max-height: 150px;
  line-height: normal;
  background-color: ivory;
}
#searchBox {
  /* width: 100%; */
  border-radius: 15px;
  border: 2px solid #198754;
  color: #198754;
  padding: 3px 15px;
  text-align: center;
}

#searchBox:focus {
  background-color: ivory;
  border: 2px solid ivory;
}

.text_input input {
  border-bottom-left-radius: 15px;
  border-bottom-right-radius: 15px;
}
.text_input span {
  border-top-left-radius: 15px;
  border-top-right-radius: 15px;
}

.lessonImage {
  max-width: 140px;
  max-height: 140px;
  transition: transform ease-in 0.6s;
}
.lessonImage:hover {
  transform: scale(1.09) rotate(-3deg);
}

.cart_image {
  font-size: 150px;
  animation-name: zoom;
  animation-duration: 2s;
  animation-timing-function: ease-in-out;
  animation-iteration-count: infinite;
}

@keyframes zoom {
  50% {
    transform: scale(1.1);
    color: ivory;
  }
}

.lesson {
  background-color: ivory;
  border: 2px solid ivory;
  border-radius: 10px;
  line-height: normal;
  animation-name: slideIn;
  animation-duration: 0.6s;
  animation-timing-function: ease-in;
}

@keyframes slideIn {
  0% {
    transform: translate(90px, 0px);
    background-color: white;
  }
}

.lesson:hover {
  border: 2px solid #198754;
}

.circle1 {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: #ffca2c;
  top: 60%;
  left: 17px;
  position: fixed;
  z-index: -6;
  animation-name: circle1;
  animation-duration: 20s;
  animation-iteration-count: infinite;
  animation-timing-function: ease-in-out;
}

.circle2 {
  width: 50px;
  height: 50px;
  position: fixed;
  border-radius: 50%;
  background-color: #198754;
  top: 70%;
  z-index: 7;
  left: 17px;
  position: fixed;
  opacity: 0.8;
  z-index: -7;
  animation-name: circle2;
  animation-duration: 40s;
  animation-iteration-count: infinite;
  animation-timing-function: ease-in-out;
  animation-direction: reverse;
}

@keyframes circle1 {
  66% {
    transform: translateY(95%);
    background-color: ivory;
  }
  80% {
    transform: scale(0.7);
  }

  33% {
    transform: translateY(-95%);
    background-color: #198754;
  }
}
@keyframes circle2 {
  66% {
    transform: translateY(-90%);
    background-color: ivory;
  }
  80% {
    transform: scale(0.7);
  }

  33% {
    transform: translateY(90%);
    background-color: #ffca2c;
  }
}
/* Shape styling  */
.shapes {
  position: relative;
}

.shape1 {
  position: fixed;
  width: 150px;
  height: 20px;
  transform: skew(25deg);
  background-color: #ffca2c;
  right: 9px;
  bottom: -8px;
  opacity: 0.5;
  animation-name: shape1;
  z-index: -7;
  animation-duration: 40s;
  animation-iteration-count: infinite;
  animation-timing-function: ease-in-out;
  animation-direction: reverse;
}

.shape2 {
  position: fixed;
  width: 150px;
  height: 20px;
  transform: skew(25deg);
  background-color: #198754;
  right: 70px;
  bottom: -8px;
  opacity: 0.7;
  z-index: -7;
  animation-name: shape2;
  animation-duration: 40s;
  animation-iteration-count: infinite;
  animation-timing-function: ease-in-out;
  animation-direction: reverse;
}
@keyframes shape1 {
  50% {
    right: 200px;
    opacity: 1;
  }
}
@keyframes shape2 {
  70% {
    right: 400px;
    opacity: 0.9;
  }
}
</style>
