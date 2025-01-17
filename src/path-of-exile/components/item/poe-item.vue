<template>
  <div v-if="show" :class="classesComputed">
    <div v-if="displayMode === `showcase`" class="poe-item-showcase-wrapper">
      <!-- Showcase -->
      <poe-item-showcase
        :item="item"
        :iconUrl="iconUrl"
        :iconSize="iconSize"
        :showIconInside="iconInside"
        :showIconOutside="iconOutside"
        :dimedSections="dimedSections"
        :hiddenSections="hiddenSections"
        :showSockets="showSocketsInShowcase"
        :socketReferences="socketReferences"
        :showBorder="showBorder"
      />
    </div>
    <div v-else class="poe-item-showcase-wrapper">
      <v-popover
        trigger="hover click"
        placement="auto"
        :offset="20"
        hideOnTargetClick
        :popoverClass="popoverClassesComputed"
        :popoverWrapperClass="popoverWrapperClasses"
        :popoverBaseClass="popoverBaseClasses"
        :popoverInnerClass="popoverInnerClasses"
        :popoverArrowClass="popoverArrowClasses"
      >
        <template slot="popover">
          <poe-item-showcase
            :item="item"
            :iconUrl="iconUrl"
            :iconSize="iconSize"
            :showIconInside="iconInside"
            :showIconOutside="iconOutside"
            :dimedSections="dimedSections"
            :hiddenSections="hiddenSections"
            :showSockets="showSocketsInShowcase"
            :socketReferences="socketReferences"
            :showBorder="showBorder"
          />
        </template>
        <!-- Icon -->
        <div v-if="displayMode === `icon`">
          <svg class="poe-item-stacks" v-if="shouldShowStacksOnIcon">
            <text x="2" y="18">{{ item.stacks }}</text>
          </svg>
          <poe-item-image
            :iconSize="iconSize"
            :iconUrl="iconUrl"
            :type="item.type"
          />
          <poe-item-sockets
            :sockets="item.sockets"
            :socketReferences="socketReferences"
            v-if="shouldShowSockets"
          />
          <div class="poe-icon-label" v-if="!showCustomLabel">
            <div>{{ labelTextComputed }}</div>
            <div class="poe-icon-sublabel" v-if="shouldShowBaseName">
              {{ item.baseName }}
            </div>
          </div>
          <div class="poe-icon-label" v-else>
            <div>
              {{ labelTextComputed }}
            </div>
          </div>
        </div>
        <!-- Text -->
        <div v-else :class="linkClassesComputed">
          {{ labelTextComputed }}
        </div>
      </v-popover>
    </div>
  </div>
</template>

<script>
import PoeItemShowcase from "./poe-item-showcase.vue";
import PoeItemImage from "./poe-item-image.vue";
import PoeItemSockets from "./poe-item-sockets.vue";
import mainMixin from "@/shared/mixins/main.mixin";
import poeEntityMixin from "./../mixins/poe-entity.mixin";

export default {
  name: "PoeItem",
  components: {
    PoeItemShowcase,
    PoeItemImage,
    PoeItemSockets,
  },
  mixins: [mainMixin, poeEntityMixin],
  props: {
    showSockets: { type: Boolean, default: false },
    showSocketsInShowcase: { type: Boolean, default: false },
  },
  computed: {
    item() {
      return this.data;
    },
    socketReferences() {
      return this.extensions ? this.extensions.socketReferences : void 0;
    },
    classesComputed() {
      let classes = `poe-item-showcase ${this.classes}`;

      switch (this.displayMode) {
        case "showcase":
          classes += " poe-item-display-showcase";
          break;
        case "icon":
          classes += " poe-item-display-icon";
          break;
        case "text":
        default:
          classes += " poe-item-display-text";
          break;
      }

      return classes;
    },
    popoverClassesComputed() {
      return `poe-item-showcase-popover ${this.popoverClasses}`;
    },
    linkClassesComputed() {
      let classes = `poe-item-link`;
      if (this.item.rarity) {
        classes += ` poe-item-link-${this.item.rarity.toLowerCase()}`;
      }
      return classes;
    },
    shouldShowBaseName() {
      return this.item.baseName && this.item.baseName != this.item.name;
    },
    shouldShowSockets() {
      return this.showSockets && this.item.sockets;
    },
  },
};
</script>

<style lang="scss">
@use "./../../_styles" as styles;

.poe-item-showcase-popover {
  z-index: 10000;
  .poe-item-wrapper {
    box-shadow: 1px 1px 20px 0px rgba(0, 0, 0, 0.5);
  }
}

.poe-item-showcase,
.poe-item-showcase-popover {
  @include styles.font;
  @include styles.colors;
  display: inline-block;
  .poe-item-link-unique {
    color: var(--poe-color-unique);
  }
  .poe-item-link-rare {
    color: var(--poe-color-rare);
  }
  .poe-item-link-magic {
    color: var(--poe-color-magic);
  }
  .poe-item-link-normal {
    color: var(--poe-color-normal);
  }
  .poe-item-link-gem {
    color: var(--poe-color-gem);
  }
  .poe-item-showcase-wrapper {
    display: flex;
  }
}
</style>
