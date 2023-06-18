<template>
  <div>
    <div class="bg-light p-3 m-2 mx-lg-5 px-lg-5 mb-4 sticky-top">
      <h4 class="m-2 text-success fw-bolder text-uppercase">Check Out:</h4>
      <!-- checkout errors -->
      <ul class="mb-3 text-success fs-6 fw-lighter">
        <li><small>Full Name field accepts only letters.</small></li>
        <li><small>Phone number field accepts only numbers.</small></li>
      </ul>
      <div
        class="input-group text_input mb-3 d-flex flex-sm-row flex-column justify-content-md-around justify-content-center align-items-center"
      >
        <div class="mx-2 p-1 flex-sm-grow-1">
          <span class="input-group-text bg-warning text-light" id="name"
            ><i class="bi me-1 bi-person-fill"></i>Full Name:
          </span>
          <input
            type="text"
            class="form-control"
            placeholder="Enter Full Name Here"
            v-model="orderData.name"
          />
        </div>

        <div class="mx-2 p-1 flex-sm-grow-1">
          <span class="input-group-text bg-warning text-light"
            ><i class="bi me-2 bi-telephone-fill"></i>Phone Number:
          </span>
          <input
            class="form-control"
            placeholder="Enter Phone Number Here."
            aria-label="phone number"
            v-model="orderData.phoneNumber"
          />
        </div>

        <div class="mx-2 p-1">
          <button
            type="button"
            class="btn-success btn btn-sm bg-gradient"
            v-if="isValid()"
            @click="submitOrder"
          >
            <i class="bi me-1 bi-check-circle-fill"></i> {{ checkMsg }}
          </button>
          <button type="button" disabled class="btn-warning btn btn-sm" v-else>
            <i class="bi me-1 bi-exclamation-circle-fill"></i>
          </button>
        </div>
      </div>
    </div>

    <!-- heading for cart page -->
    <h4 class="text-center fw-bolder text-uppercase pt-2 text-success">
      Cart List
    </h4>

    <div
      class="empty_box d-flex flex-column justify-content-around align-items-center p-4"
      v-if="Object.keys(cart).length <= 0"
    >
      <i class="bi mx-auto my-auto cart_image bi-cart-x-fill text-success"></i>

      <p class="text-success h4">Empty!!</p>
    </div>

    <!-- shopping cart box -->
    <div
      class="resultBox container d-flex justify-content-around align-items-center flex-wrap"
    >
      <div
        class="d-flex m-2 lesson p-3"
        v-for="lesson in getCartLessons"
        :key="lesson._id"
      >
        <div class="d-flex justify-content-center align-items-center">
          <div class="me-2">
            <figure>
              <img class="lessonImage" :src="server_url + lesson.image" />
            </figure>
          </div>
          <div class="ms-1 h-90">
            <p><strong>Subject: </strong>{{ lesson.subject }}</p>
            <p><strong>Location: </strong>{{ lesson.location }}</p>
            <p><strong>Price: </strong>AED {{ lesson.price }}.00</p>
            <p><strong>Spaces: </strong>{{ lesson.space }}</p>

            <button
              class="btn btn-success btn-sm"
              @click="removeFromCart(lesson)"
            >
              <i class="bi bi-trash3-fill me-1"></i> Remove
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "CheckoutComponent",
  props: ["cart", "checkMsg", "lessons"], //registers expected props from the parent component
  data() {
    return {
      server_url: "http://localhost:3000/", //stores server url
      cartArray: [],
      orderData: {
        name: "",
        phoneNumber: "",
      },
    };
  },
  computed: {
    getCartLessons() {
      const init = () => (this.cartArray = []);
      init();
      //updates the cart array with information about the activity
      const updateArray = (id) => {
        let lessonObj = { id: id },
          cartObj;

        cartObj = this.lessons.find((obj) => obj.id == id);
        lessonObj.subject = cartObj.subject;
        lessonObj.location = cartObj.location;
        lessonObj.price = cartObj.price;
        lessonObj.image = cartObj.image;
        lessonObj.space = this.cart[id];

        //adds object to the cartArray
        this.cartArray.push(lessonObj);
      };
      Object.keys(this.cart).forEach(updateArray);
      return this.cartArray;
    },
  },
  methods: {
    removeFromCart(lesson) {
      // updates the cart array within the component
      this.cartArray.forEach((c_lesson, ix) => {
        if (c_lesson.id === lesson.id) {
          // console.log(this.cartArray[ix]);
          this.cartArray.splice(ix, 1);
        }
      });
      //emits an event to the parent component to update the cart
      this.$emit("removeFromCart", lesson);
    },
    isValid() {
      //checks if the  phone and name fields are valid
      if (this.orderData.phoneNumber === "" || this.orderData.name === "") {
        return false;
      }
      if (
        this.orderData.phoneNumber.search(/[a-z]/gi) > -1 ||
        this.orderData.name.search(/[0-9]/g) > -1
      ) {
        return false;
      } else if (this.cartArray.length < 1) {
        return false;
      } else {
        return true;
      }
    },
    submitOrder() {
      //emits event to the root component to submit order
      this.$emit("submitOrder", this.orderData);
    },
  },
};
</script>
