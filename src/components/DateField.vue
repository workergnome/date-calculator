<!-- ###################   HTML   ################### -->
<template>
  <div>
    <div class="overlay_holder">
      <span class="overlay" v-html="message_highlight"></span>
      <b-field label="Date Phrase" :type="has_error" :message="error_message">
        <b-input v-model="message" placeholder="enter a date or date phrase">
        </b-input>
      </b-field>
    </div>
    <DateVisualization :data="linked_art" />

    <JsonViewer v-if="debug" :data="json_data" />
  </div>
</template>

<!-- ################### JAVACRIPT ################### -->

<script>
import JsonViewer from "./JsonViewer.vue";
import DateVisualization from "./DateVisualization.vue";

import { parse_date } from "art-tracks";

export default {
  components: {
    JsonViewer,
    DateVisualization
  },
  props: {
    url: {
      type: String,
      default: "#timespan"
    },
    debug: {
      type: Boolean,
      default: false
    }
  },
  data: () => ({
    message: ""
  }),
  computed: {
    // a computed getter
    message_highlight: function() {
      if (!this.has_error) return null;
      let offset = this.response.error.offset;
      let msg = this.message;
      let valid_text = msg.substr(0, offset);
      let invalid_text = msg.substr(offset, msg.length);
      return `${valid_text}<span class='is-error'>${invalid_text}</span>`;
    },
    has_error: function() {
      return this.error_message ? "is-danger" : "";
    },
    linked_art: function() {
      return this.response ? this.response.linked_art : {};
    },
    error_message: function() {
      if (this.response && this.response.error) {
        return this.computeErrorMessage(this.response.error);
      }
      return null;
    },
    json_data: function() {
      return this.response ? JSON.stringify(this.response, null, 2) : "";
    },
    response: function() {
      return parse_date(this.message, { url: this.url });
    }
  },
  methods: {
    computeErrorMessage: function(m) {
      return `Unexpected character: <b>${m.token.value}</b>`;

      // let lines = m.message
      //   .trim()
      //   .split("\n")
      //   .filter(line => line);

      // let syntax = lines.shift();
      // let test = lines.shift();
      // let caret = lines.shift();
      // let msg = lines.join(" ");

      // return msg;

      //let example = `${test}<br>${caret}`.replace(/ /g, "&nbsp;");
      //`<span class='is-block is-family-monospace error-text'>${example}</span>`;
    }
  }
};
</script>

<!-- ###################    CSS    ################### -->
<style lang="scss">
.overlay_holder {
  position: relative;
}

.overlay {
  color: rgba(0, 0, 0, 0);
  padding-left: 0.625em;
  position: absolute;
  top: calc(22px + 1em);
  z-index: 1;
}

.is-error {
  background: rgba(255, 120, 120, 0.5);
}
</style>
