<template>
  <div>
    <!-- controls box  -->
    <div
      class="container p-4 bg-light controlBox d-flex flex-column flex-lg-row justify-content-around align-items-center"
    >
      <div class="searchBox d-flex flex-column flex-sm-grow-1">
        <h6 class="mb-2">Search:</h6>
        <input
          id="searchBox"
          type="text"
          placeholder="Enter Search Term"
          v-model="searchTerm"
          @input="() => handle_lessons()"
        />
      </div>

      <div class="toolBox container">
        <!-- sort section  -->
        <div class="sortBox d-flex flex-column m-3 p-2 justify-content-between">
          <h6 class="mb-2">Sort:</h6>

          <div class="btn-group btn-group-sm" role="group">
            <input
              class="btn-check"
              type="radio"
              name="attribute"
              id="subject"
              autocomplete="off"
              value="subject"
              checked
              v-model="sortAttr"
            />
            <label class="btn btn-outline-success" for="subject">Subject</label>
            <input
              class="btn-check"
              name="attribute"
              type="radio"
              id="location"
              autocomplete="off"
              value="location"
              v-model="sortAttr"
            />
            <label class="btn btn-outline-success" for="location"
              >Location</label
            >
            <input
              class="btn-check"
              name="attribute"
              type="radio"
              id="price"
              value="price"
              v-model="sortAttr"
              autocomplete="off"
            />
            <label class="btn btn-outline-success" for="price">Price</label>
            <input
              class="btn-check"
              name="attribute"
              type="radio"
              id="available"
              value="availableSpace"
              v-model="sortAttr"
              autocomplete="off"
            />
            <label class="btn btn-outline-success" for="available"
              >Availablity</label
            >
          </div>
          <div class="mt-3 btn-group btn-group-sm" role="group">
            <input
              class="btn-check"
              type="radio"
              id="ascending"
              autocomplete="off"
              value="ascending"
              name="order"
              checked
              v-model="order"
            />
            <label class="btn-outline-success btn" for="ascending"
              ><i class="bi bi-arrow-up me-1"></i>Ascending</label
            >
            <input
              type="radio"
              class="btn-check"
              id="descending"
              value="descending"
              v-model="order"
              autocomplete="off"
              name="order"
            />
            <label class="btn-outline-success btn" for="descending"
              ><i class="bi bi-arrow-down me-1"></i>Descending</label
            >
          </div>
        </div>
      </div>
    </div>

    <!-- result box  -->
    <div
      class="resultBox container d-flex justify-content-around align-items-center flex-wrap"
    >
      <div
        class="d-flex m-2 lesson p-3"
        v-for="lesson in sortedLessons"
        :key="lesson.id"
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
            <p><strong>Spaces: </strong>{{ lesson.availableSpace }}</p>

            <button
              v-if="canAddToCart(lesson)"
              class="btn btn-success btn-sm bg-gradient"
              @click="addToCart(lesson)"
            >
              <i class="bi bi-plus-lg me-1"></i> Add To cart
            </button>
            <button
              v-else
              type="button"
              disabled
              class="btn btn-success btn-sm"
            >
              <i class="bi bi-exclamation-circle-fill me-1"></i> Add To Cart
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "LessonsComponent",
  props: ["lessons", "cart"], //registers expected props from the parent component
  data() {
    return {
      searchTerm: "",
      sortAttr: "subject",
      order: "ascending",
      server_url: "http://localhost:3000/", // saves server url for retrieving images
    };
  },
  methods: {
    handle_lessons() {
      this.$emit("handle_lessons", this.searchTerm);
    },
    canAddToCart(lesson) {
      if (lesson.availableSpace === 0) return false;
      if (this.cart[lesson.id] === undefined) {
        return true;
      }
      return lesson.availableSpace > 0;
    },
    addToCart(lesson) {
      //emits an event to the event to add a lesson to the cart
      this.$emit("addToCart", lesson);
    },
  },
  computed: {
    sortedLessons() {
      //computed property to implement sort functionality
      let sorted_lessons = this.lessons.slice(0);

      const check = (a) => {
        if (typeof a === String) {
          return a.toLowerCase();
        } else {
          return a;
        }
      };

      // sort function
      const compare = (a, b) => {
        if (check(a[this.sortAttr]) < check(b[this.sortAttr])) {
          return -1;
        }
        if (check(a[this.sortAttr]) < check(b[this.sortAttr])) {
          return 0;
        }
      };

      if (this.order === "descending") {
        return sorted_lessons.sort(compare).reverse();
      }
      return sorted_lessons.sort(compare);
    },
  },
};
</script>
