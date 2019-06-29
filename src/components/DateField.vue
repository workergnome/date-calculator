<!-- ###################   HTML   ################### -->
<template>
  <div class="overlay_holder">
    <span class="overlay" v-html="message_highlight"></span>
    <b-field label="Date Phrase" :type="hasError" :message="error_message">
      <b-input v-model="message" placeholder="enter a date or date phrase">
      </b-input>
    </b-field>

    <div class="box" v-if="debug">
      <pre>{{ json_data }}</pre>
    </div>
  </div>
</template>

<!-- ################### JAVACRIPT ################### -->

<script>
import { parse_date } from "art-tracks";

export default {
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
      if (!this.hasError) return null;
      let offset = this.response.error.offset;
      let msg = this.message;
      let valid_text = msg.substr(0, offset);
      let invalid_text = msg.substr(offset, msg.length);
      return `${valid_text}<span class='is-error'>${invalid_text}</span>`;
    },
    hasError: function() {
      return this.error_message ? "is-danger" : "";
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
      let lines = m.message
        .trim()
        .split("\n")
        .filter(line => line);

      let syntax = lines.shift();
      let test = lines.shift();
      let caret = lines.shift();
      let msg = lines.join(" ");

      return msg;

      //let example = `${test}<br>${caret}`.replace(/ /g, "&nbsp;");
      //`<span class='is-block is-family-monospace error-text'>${example}</span>`;
    }
  }
};
</script>

<!-- ###################    CSS    ################### -->
<style lang="scss">
.error-text {
  font-weight: bold;
}
.overlay {
  padding-left: 0.625em;
  top: calc(22px + 1em);
  position: absolute;
  z-index: 1;
  color: rgba(0, 0, 0, 0);
}
.overlay_holder {
  position: relative;
}
.is-error {
  background: rgba(255, 120, 120, 0.5);
  color: rgba(0, 0, 0, 0);
  padding: 1px;
  margin-left: -1px;
}
</style>
