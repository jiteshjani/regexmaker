<template>
  <q-page class="flex bg-grey-2">
    <div class="col q-pa-md q-gutter-sm">
      <q-card class="col my-card">
        <q-card-section>
          <div class="row">
            <div class="col-xs-12 col-sm-6 q-pa-sm">
              <q-input outlined bottom-slots v-model="before" label="Before the required string">
                <template v-if="!isES9Supported" v-slot:hint>
                  <span
                    class="text-orange-9"
                  >** This feature might not work as expected without ES9 support.</span>
                </template>
              </q-input>
            </div>
            <div class="col-xs-12 col-sm-6 q-pa-sm">
              <q-input outlined v-model="after" label="After the required string" />
            </div>
          </div>

          <div class="row">
            <div class="col-xs-12 col-sm-6 q-pa-sm">
              <q-input outlined v-model="startWith" label="Required string starts with" />
            </div>
            <div class="col-xs-12 col-sm-6 q-pa-sm">
              <q-input outlined v-model="endWith" label="Required string ends with" />
            </div>
          </div>

          <div class="row">
            <div class="col-xs-12 col-sm-6 q-pa-sm">
              <strong>Modifiers:</strong>
              <div class="col q-gutter-sm">
                <q-checkbox v-model="global" label="Global" />
                <q-checkbox v-model="caseInsensitive" label="Case Insensitive" />
                <q-checkbox v-model="multiLine" label="Multi-Line" />
                <q-checkbox v-model="unicode" label="Unicode" />
                <q-checkbox v-model="sticky" label="Sticky" />
              </div>
            </div>
            <div class="col-xs-12 col-sm-6 q-pa-sm">
              <strong>Additional Settings:</strong>
              <div class="col q-gutter-sm">
                <q-checkbox v-model="autoEscape" label="Auto Escape Input Values">
                  <q-tooltip content-class="bg-warning text-grey-8" content-style="font-size: 16px">
                    If you are conformable with regex and want to use special
                    characters in the input fields, then uncheck this
                    option.
                  </q-tooltip>
                </q-checkbox>
                <q-checkbox
                  v-if="isES9Supported"
                  v-model="enableES9"
                  label="Enable ES9 Regex Features"
                >
                  <q-tooltip content-class="bg-warning text-grey-8" content-style="font-size: 16px">
                    ES8 regex features are still not supported by many browsers,
                    so your regular expression might not work on different
                    devices.
                  </q-tooltip>
                </q-checkbox>
              </div>
            </div>
          </div>

          <div class="column">
            <div class="col q-px-sm">
              <strong>Regex Pattern:</strong>
            </div>
            <div class="col q-pa-sm">
              <q-input outlined v-model="regexPattern" class="bg-orange-1" readonly>
                <template v-slot:append>
                  <q-btn @click="copyRegex" class="text-blue-8" round dense flat icon="file_copy">
                    <q-tooltip>Copy Regular Expression</q-tooltip>
                  </q-btn>
                </template>
              </q-input>
            </div>
          </div>

          <div class="row">
            <div class="col-xs-12 col-sm-6 q-px-sm">
              <strong>Sample Text:</strong>
              <q-input v-model="sampleText" filled outlined type="textarea" />
            </div>

            <div class="col-xs-12 col-sm-6 q-px-sm">
              <strong>Matches:</strong>
              <q-input v-model="matchText" filled outlined type="textarea" readonly />
            </div>
          </div>
        </q-card-section>
      </q-card>
    </div>
  </q-page>
</template>

<script>
import { copyToClipboard } from "quasar";

export default {
  name: "PageIndex",
  data() {
    return {
      before: "",
      after: "",
      startWith: "",
      endWith: "",
      sampleText: "",
      expertMode: false,
      global: true,
      caseInsensitive: true,
      multiLine: true,
      unicode: false,
      sticky: false,
      autoEscape: true,
      enableES9: false,
      toolbar: []
    };
  },
  computed: {
    isES9Supported: function() {
      try {
        let re = new RegExp("(?<=foo)bar", this.flags);
        return true;
      } catch (e) {
        return false;
      }
    },
    flags: function() {
      let flags = "";
      if (this.global) flags += "g";
      if (this.caseInsensitive) flags += "i";
      if (this.multiLine) flags += "m";
      if (this.unicode) flags += "u";
      if (this.sticky) flags += "y";
      return flags;
    },
    regexPattern: function() {
      let pattern = "";
      if (this.before) {
        if (this.enableES9) {
          pattern += "(?<=" + this.escapeRegex(this.before) + ")";
        } else {
          pattern += "(?![" + this.escapeRegex(this.before) + "])";
        }
      }
      if (this.startWith) pattern += this.escapeRegex(this.startWith);
      pattern += ".+";
      if (this.endWith) pattern += this.escapeRegex(this.endWith);
      if (this.after) pattern += "(?=" + this.escapeRegex(this.after) + ")";

      return new RegExp(pattern, this.flags);
    },
    matchText: function() {
      let regex = this.regexPattern;
      let matches = "";
      let matchArr;
      matchArr = this.sampleText.match(regex);
      if (matchArr !== null) {
        matchArr.forEach(match => {
          if (match !== "") matches += `${match}\n`;
        });
      }
      return matches;
    }
  },
  methods: {
    escapeRegex: function(string) {
      if (this.autoEscape) {
        return string.replace(/[.*+?^${}()|[\]\\]/g, "\\$&");
      } else {
        return string.replace(
          /\\(?!(b|B|c|d|D|f|n|r|s|S|t|v|w|W|n|0|x|u))/g,
          "\\$&"
        );
      }
    },
    copyRegex: function() {
      copyToClipboard(this.regexPattern)
        .then(() => {
          this.$q.notify({
            position: "top-right",
            message: "Regex copied to your clipboard",
            color: "positive"
          });
        })
        .catch(() => {
          this.$q.notify({
            message: "Could not copy to clipboard",
            color: "negative"
          });
        });
    }
  }
};
</script>
