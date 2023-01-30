<template>
  <div>
    <div>
      <input type="checkbox" :checked="isEditable" @change="() => isEditable = !isEditable">
      Editable
    </div>
    <bubble-menu
        class="bubbleMenu"
        :editor="editor"
        :tippy-options="{ duration: 100 }"
        v-if="editor"
    >
      <button class="bubbleMenuButton" @click="editor.chain().focus().toggleBold().run()" :class="{ 'is-active': editor.isActive('bold') }">
        Bold
      </button>
      <button class="bubbleMenuButton" @click="editor.chain().focus().toggleItalic().run()" :class="{ 'is-active': editor.isActive('italic') }">
        Italic
      </button>
      <button class="bubbleMenuButton" @click="editor.chain().focus().toggleStrike().run()" :class="{ 'is-active': editor.isActive('strike') }">
        Strike
      </button>
      <button class="bubbleMenuButton" @click="editor.commands.toggleHeading({ level: 1 })" :class="{ 'is-active': editor.isActive('heading') }">
        H1
      </button>
      <button class="bubbleMenuButton" @click="editor.commands.toggleHeading({ level: 2 })" :class="{ 'is-active': editor.isActive('heading') }">
        H2
      </button>
    </bubble-menu>
    <editor-content :editor="editor"/>
  </div>
</template>

<script>
import {BubbleMenu, Editor, EditorContent} from '@tiptap/vue-3'
import {StarterKit} from "@tiptap/starter-kit";
import {Heading} from "@tiptap/extension-heading";
import {Mention} from "@tiptap/extension-mention";
import suggestion from '../js/suggestion'

export default {
  components: {
    EditorContent,
    BubbleMenu,
  },

  data() {
    return {
      editor: null,
      isEditable: true,
    }
  },

  watch: {
    isEditable(value) {
      this.editor.setEditable(value)
    },
  },

  mounted() {
    this.editor = new Editor({
      extensions: [
        StarterKit,
        Heading.configure({
          levels: [1, 2],
        }),
        Mention.configure({
          HTMLAttributes: {
            class: 'mention',
          },
          suggestion,
        }),
      ],
      content: `
        <p>Hi <span data-type="mention" data-id="Esmaili Reza"></span>!</p>
        <p>Hallo <span data-type="mention" data-id="Roth Lukas"></span> und <span data-type="mention" data-id="Kuntz Loïc"></span>!</p>
      `,
    })
  },

  beforeUnmount() {
    this.editor.destroy()
  },
}
</script>

<style>
/* Basic editor styles */
.ProseMirror > * + * {
  margin-top: 0.75em;
  background-color: white;
}

.bubbleMenu {
  border: 2px solid black;
  border-radius: 4px;
}

.bubbleMenuButton {
  background-color: white;
  padding: 0.5em 1em;
  border: none;
  color: black;
  transition-duration: 0.5s;

  cursor: pointer;
}

.bubbleMenuButton:hover {
  background-color: #ddd;
}

input[type="checkbox"] {
  margin-right: 4px;
}

.mention {
  border: 1px solid #c7c7c7;
  border-radius: 0.4rem;
  padding: 0.1rem 0.3rem;
  box-decoration-break: clone;
  transition-duration: 0.5s;
}

.mention:hover {
  background-color: #f1f1f1;
}
</style>