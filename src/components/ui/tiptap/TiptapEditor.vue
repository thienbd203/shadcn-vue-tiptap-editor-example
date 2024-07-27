<script setup lang="ts">
import { useEditor, EditorContent, BubbleMenu, Editor } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'
import { Popover, PopoverContent, PopoverTrigger } from '@/components/ui/popover'
import IconLucideComponent from '@/components/ui/icons/IconLucideComponent.vue'
import { Toggle } from '@/components/ui/toggle'
import { icons } from 'lucide-vue-next'
import { ref } from 'vue'

type Level = 1 | 2 | 3 | 4 | 5 | 6

const levels: Level[] = [1, 2, 3, 4, 5, 6]
const iconStrokeWidth = ref<number>(2)

const editor = useEditor({
  content: `Tiptap content`,
  extensions: [StarterKit],
  editorProps: {
    attributes: {
      class: 'prose prose-sm sm:prose-base lg:prose-lg xl:prose-2xl m-5 focus:outline-none'
    }
  }
})

function getPopoverContentClass(): string {
  return 'w-[151px] flex flex-col gap-1 px-2 py-4 bg-white rounded-lg dark:bg-black shadow-sm border border-neutral-200 shadow-sm'
}

function getToggleStyle(): string {
  return 'text-muted-foreground hover:text-accent-foreground'
}

function getTriggerContentTypography(editor: Editor): keyof typeof icons {
  if (editor.isActive('bulletList')) {
    return 'List'
  }
  if (editor.isActive('orderedList')) {
    return 'ListOrdered'
  }
  if (editor.isActive('heading', { level: 1 })) {
    return 'Heading1'
  }
  if (editor.isActive('heading', { level: 2 })) {
    return 'Heading2'
  }
  if (editor.isActive('heading', { level: 3 })) {
    return 'Heading3'
  }
  return 'Pilcrow'
}

function getToggleClassInSidePopoverContent() {
  return 'flex items-center gap-2 p-1.5 text-sm !justify-start'
}
</script>

<template>
  <bubble-menu
    v-if="editor"
    :editor="editor"
    :tippy-options="{ duration: 100 }"
    class="bubble-menu border rounded-md shadow-sm p-1 flex items-center space-x-1"
  >
    <Popover>
      <PopoverTrigger class="flex items-center space-x-2">
        <Toggle size="xs" :pressed="true">
          <IconLucideComponent
            :name="getTriggerContentTypography(editor)"
            class="w-5 h-5"
            :stroke-width="iconStrokeWidth"
          />
          <IconLucideComponent name="ChevronDown" class="w-3 h-3" :stroke-width="iconStrokeWidth" />
        </Toggle>
      </PopoverTrigger>
      <PopoverContent :class="getPopoverContentClass()">
        <div class="space-y-3">
          <div
            class="text-[.65rem] font-semibold mb-1 uppercase text-neutral-500 dark:text-neutral-400 px-1.5"
          >
            Hierarchy
          </div>
          <div class="space-y-1 flex flex-col">
            <Toggle
              :pressed="editor.isActive('paragraph')"
              @click="editor.chain().focus().setParagraph().run()"
              :class="getToggleStyle() + ' ' + getToggleClassInSidePopoverContent()"
            >
              <IconLucideComponent
                name="Pilcrow"
                class="w-5 h-5 mr-1"
                :stroke-width="iconStrokeWidth"
              />
              Paragraph
            </Toggle>
            <Toggle
              v-for="level in levels"
              :key="level"
              :class="getToggleStyle() + ' ' + getToggleClassInSidePopoverContent()"
              :pressed="editor.isActive('heading', { level })"
              @click="editor.chain().focus().toggleHeading({ level }).run()"
            >
              <IconLucideComponent
                :name="`Heading${level}` as keyof typeof icons"
                class="w-5 h-5 mr-1"
                :stroke-width="iconStrokeWidth"
              />
              Heading {{ level }}
            </Toggle>
          </div>
        </div>
        <div class="space-y-2">
          <div
            class="text-[.65rem] font-semibold mb-1 uppercase text-neutral-500 dark:text-neutral-400 px-1.5"
          >
            LISTS
          </div>
          <div class="space-y-1 flex flex-col">
            <Toggle
              :pressed="editor.isActive('bulletList')"
              @click="editor.chain().focus().toggleBulletList().run()"
              :class="getToggleStyle() + ' ' + getToggleClassInSidePopoverContent()"
            >
              <IconLucideComponent
                name="List"
                class="w-5 h-5 mr-1"
                :stroke-width="iconStrokeWidth"
              />
              Bullet List
            </Toggle>
            <Toggle
              :pressed="editor.isActive('orderedList')"
              @click="editor.chain().focus().toggleOrderedList().run()"
              :class="getToggleStyle() + ' ' + getToggleClassInSidePopoverContent()"
            >
              <IconLucideComponent
                name="ListOrdered"
                class="w-5 h-5 mr-1"
                :stroke-width="iconStrokeWidth"
              />
              Ordered List
            </Toggle>
          </div>
        </div>
      </PopoverContent>
    </Popover>
    <Toggle
      :class="getToggleStyle()"
      :pressed="editor.isActive('bold')"
      @click="editor.chain().focus().toggleBold().run()"
      :disabled="!editor.can().chain().focus().toggleBold().run()"
      size="xs"
    >
      <IconLucideComponent name="Bold" class="w-5 h-5" :stroke-width="iconStrokeWidth" />
    </Toggle>
    <Toggle
      :class="getToggleStyle()"
      :pressed="editor.isActive('italic')"
      @click="editor.chain().focus().toggleItalic().run()"
      :disabled="!editor.can().chain().focus().toggleItalic().run()"
      size="xs"
    >
      <IconLucideComponent name="Italic" class="w-5 h-5" :stroke-width="iconStrokeWidth" />
    </Toggle>
    <Toggle
      :class="getToggleStyle()"
      :pressed="editor.isActive('strike')"
      @click="editor.chain().focus().toggleStrike().run()"
      :disabled="!editor.can().chain().focus().toggleStrike().run()"
      size="xs"
    >
      <IconLucideComponent name="Strikethrough" class="w-5 h-5" :stroke-width="iconStrokeWidth" />
    </Toggle>
  </bubble-menu>
  <editor-content :editor="editor" />
</template>
