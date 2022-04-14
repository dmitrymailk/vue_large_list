<template>
  <div class="wrapper" @scroll="scrollEvent" ref="wrapperElem">
    <Card
      v-for="(item, index) in centerItems"
      :key="index"
      :title="item.title"
      :text="item.text"
      :mountedCallback="mountedCallback"
    />
    <Card
      v-for="(item, index) in bottomItems"
      :key="index"
      :title="item.title"
      :text="item.text"
      :mountedCallback="mountedCallback"
    />
  </div>
</template>

<script>
import Card from "./components/Card.vue";
// You can import any component you want as a named export from 'vue-virtualised'. e.g.

export default {
  components: {
    Card,
  },
  data() {
    this.allItems = [];
    return {
      items: [],
      upItems: [],
      centerItems: [],
      bottomItems: [],
      currentItem: 0,
      deltaItem: 100,
      totalHeight: 0,
    };
  },
  methods: {
    generateLargeList() {
      const amount = Math.pow(10, 5);
      // const amount = 30;
      let items = [];
      for (let i = 0; i < amount; i++) {
        let text = "";
        const words_amount = this.generateRandom(3, 30);
        for (let j = 0; j < words_amount; j++) text += " random text";
        let item_data = {
          title: `Title ${i}`,
          text: text,
        };
        items.push(item_data);
      }

      this.allItems = items;
      this.bottomItems = items.slice(0, this.deltaItem);
    },
    generateRandom(min = 0, max = 100) {
      let difference = max - min;
      let rand = Math.random();
      rand = Math.floor(rand * difference);
      rand = rand + min;
      return rand;
    },
    mountedCallback(height) {
      this.totalHeight += height;
    },
    scrollEvent(e) {
      // console.log(e);
      const wrapperElem = this.$refs.wrapperElem;
      const scrollProgress =
        wrapperElem.scrollTop /
        (wrapperElem.scrollHeight - wrapperElem.clientHeight);
      // console.log(scrollProgress);
      let overflow = Math.floor(this.allItems.length / this.deltaItem - 1);
      if (scrollProgress >= 0.98 && this.currentItem < overflow) {
        console.log("end");
        this.currentItem += 1;
        let cur = this.currentItem;
        this.centerItems = this.bottomItems;
        this.bottomItems = this.allItems.slice(
          cur * this.deltaItem,
          (cur + 1) * this.deltaItem
        );
        let centerId = 0;
        if (this.currentItem == 1) {
          // console.log(wrapperElem.children.length);
          centerId = 1;
        } else centerId = this.deltaItem - 1;
        console.log(centerId);
        let lastCenter = wrapperElem.children[centerId];
        lastCenter.scrollIntoView();
      } else if (scrollProgress <= 0.01 && this.currentItem > 1) {
        console.log("start");
        this.currentItem -= 1;
        let cur = this.currentItem;
        this.bottomItems = this.centerItems;
        this.centerItems = this.allItems.slice(
          (cur - 1) * this.deltaItem,
          cur * this.deltaItem
        );

        let lastCenter = wrapperElem.children[this.deltaItem - 1];
        lastCenter.scrollIntoView();
      }
    },
  },

  mounted() {
    console.log("mounted");
    this.generateLargeList();
  },
};
</script>

<style>
.card {
  width: 300px;
  border-radius: 8px;
  border: 1px solid black;
  margin-bottom: 10px;
}

.wrapper {
  overflow-y: scroll;
  max-height: 300px;
  width: 501px;
  margin-top: 0px;
}
</style>
