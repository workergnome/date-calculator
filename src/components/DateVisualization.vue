<template>
  <div ref="svgholder" class="box">
    <svg :width="maxwidth" height="80">
      <g class="axis" transform="translate(0,11)">
        <rect :width="maxwidth" height="1"></rect>
        <text class="axis-text" x="8" transform="rotate(75)">
          {{ leftAxis }}
        </text>
        <g :transform="`translate(${maxwidth - 20},0)`">
          <text class="axis-text" x="8" :transform="`rotate(75)`">
            {{ rightAxis }}
          </text>
        </g>
      </g>
      <rect
        class="possible-bounds"
        :width="pos_width"
        height="10"
        y="0"
        :x="pos_xpos"
      ></rect>
      <rect :width="def_width" height="10" :x="def_xpos"></rect>
      <g v-html="axis"></g>
    </svg>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  data: function() {
    return { maxwidth: 0 };
  },
  props: { data: { type: Object } },
  updated: function() {
    this.$nextTick(function() {
      console.log(this.$refs.svgholder.offsetWidth); //.svgholder.offsetWidth;
      this.maxwidth = this.$refs.svgholder.offsetWidth - 40;
    });
  },
  computed: {
    leftAxis: function() {
      let year = this.xscale.invert(0).getFullYear();
      return year || "";
    },
    rightAxis: function() {
      let year = this.xscale.invert(this.maxwidth).getFullYear();
      return year || "";
    },

    def_width: function() {
      let d = null;
      if ((d = this.definite)) {
        let w = d[1] - d[0];
        if (w >= 0) {
          return w;
        }
      }
      return 0;
    },
    pos_width: function() {
      let d = null;
      if ((d = this.possible)) {
        let w = d[1] - d[0];
        if (w >= 0) {
          return w;
        }
      }
      return 0;
    },
    def_xpos: function() {
      return this.definite ? this.definite[0] : 0;
    },
    pos_xpos: function() {
      return this.possible ? this.possible[0] : 0;
    },
    axis: function() {
      // return d3.axisBottom(x).ticks(10);
    },
    dates: function() {
      if (!this.data || !this.data.timespan) {
        return [];
      }
      let ts = this.data.timespan;

      let botb = ts.begin_of_the_begin ? new Date(ts.begin_of_the_begin) : null;
      let eotb = ts.end_of_the_begin ? new Date(ts.end_of_the_begin) : null;
      let bote = ts.begin_of_the_end ? new Date(ts.begin_of_the_end) : null;
      let eote = ts.end_of_the_end ? new Date(ts.end_of_the_end) : null;

      let dates = [botb, eotb, bote, eote];
      return dates;
    },
    xscale: function() {
      let width = this.maxwidth;

      let x = d3.scaleTime();
      x.range([0, width]);
      x.domain([this.dates[0], new Date(Date.now())]);
      x.nice(d3.timeYear, 5);
      return x;
    },
    definite: function() {
      const eotb = this.dates[1];
      const bote = this.dates[2];
      return eotb && bote ? [this.xscale(eotb), this.xscale(bote)] : null;
    },
    possible: function() {
      const botb = this.dates[0];
      const eote = this.dates[3];
      return botb && eote ? [this.xscale(botb), this.xscale(eote)] : null;
    }
  }
  // render: function(h, context) {
  //   let data = context.props.data;

  //     let width = 800;

  //     var x = d3.scaleTime();
  //     x.range([0, width]);
  //     x.domain([dates[0], new Date(2020, 1, 1)]);
  //     x.nice(d3.timeYear, 50);

  //     let definite = eotb && bote ? [x(eotb), x(bote)] : null;
  //   }
  //   return h(
  //     "div",
  //     { class: "box", data: context.data, props: { definite: definite } },
  //     [
  //       h("svg", { ref: "d3Obj", props: { definite: context.definite } }, [
  //         h("rect", {
  //           height: 5,
  //           width: definite[1] - definite[0],
  //           x: definite[0]
  //         })
  //       ])
  //     ]
  //   );
  // }
};
</script>

<!-- ###################    CSS    ################### -->
<style scoped lang="scss">
.possible-bounds {
  fill: lighten(desaturate(#428bca, 30), 10);
}
.axis-text {
  font-size: 80%;
}
</style>
