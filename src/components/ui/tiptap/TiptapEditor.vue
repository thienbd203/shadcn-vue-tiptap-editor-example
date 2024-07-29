<script setup lang="ts">
import { useEditor, EditorContent, BubbleMenu, Editor } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'
import { Popover, PopoverContent, PopoverTrigger } from '@/components/ui/popover'
import IconLucideComponent from '@/components/ui/icons/IconLucideComponent.vue'
import { Toggle, type ToggleVariants } from '@/components/ui/toggle'
import { icons } from 'lucide-vue-next'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Link } from '@tiptap/extension-link'

type Level = 1 | 2 | 3 | 4 | 5 | 6

const levels: Level[] = [1, 2, 3, 4, 5, 6]

const bubbleIconConfig = {
  strokeWidth: 2,
  class: 'w-4 h-4'
}

const labelInsidePopoverContentConfig = {
  class: 'text-[.65rem] font-semibold mb-1 uppercase text-neutral-500 dark:text-neutral-400 px-1.5'
}
const bubbleButtonSize = 'sm'

const bubbleToggleConfig = {
  class: 'text-muted-foreground hover:text-accent-foreground',
  size: bubbleButtonSize as ToggleVariants['size']
}

const bubbleToggleInsidePopoverContentConfig = {
  class:
    'text-muted-foreground hover:text-accent-foreground flex items-center gap-2 p-1.5 text-sm !justify-start',
  size: bubbleButtonSize as ToggleVariants['size']
}

const editor = useEditor({
  content: `Tiptap content`,
  extensions: [
    StarterKit,
    Link.configure({
      openOnClick: false
    })
  ],
  editorProps: {
    attributes: {
      class: 'prose prose-sm sm:prose-base lg:prose-lg xl:prose-2xl m-5 focus:outline-none'
    }
  }
})

const popoverContentHierarchyConfig = {
  class:
    'w-[151px] flex flex-col gap-1 px-2 py-4 bg-white rounded-lg dark:bg-black shadow-sm border border-neutral-200 shadow-sm'
}

const popoverContentLinkConfig = {
  class:
    'w-[350px] flex flex-col gap-1 px-2 py-4 bg-white rounded-lg dark:bg-black shadow-sm border border-neutral-200 shadow-sm'
}

function setLink(event: Event) {
  const target = event.target as HTMLFormElement
  const linkInput = target.elements.namedItem('link') as HTMLInputElement

  if (linkInput.value) {
    editor.value?.chain().focus().setLink({ href: linkInput.value }).run()
  }

  return
}

function getTriggerContentTypography(editor: Editor): keyof typeof icons {
  const types = [
    { check: 'bulletList', icon: 'List' },
    { check: 'orderedList', icon: 'ListOrdered' },
    { check: 'heading', level: 1, icon: 'Heading1' },
    { check: 'heading', level: 2, icon: 'Heading2' },
    { check: 'heading', level: 3, icon: 'Heading3' },
    { check: 'heading', level: 4, icon: 'Heading4' },
    { check: 'heading', level: 5, icon: 'Heading5' },
    { check: 'heading', level: 6, icon: 'Heading6' }
  ]

  for (const type of types) {
    if (editor.isActive(type.check, type.level !== undefined ? { level: type.level } : {})) {
      return type.icon as keyof typeof icons
    }
  }

  return 'Pilcrow'
}

function handleViewLink(link: string) {
  if (link) {
    const a = document.createElement('a')
    a.href = link
    a.target = '_blank'
    a.rel = 'noopener noreferrer' // Thêm thuộc tính bảo mật
    a.click()
  }
}
</script>

<template>
  <bubble-menu
    v-if="editor"
    :editor="editor"
    :tippy-options="{ duration: 100 }"
    class="bubble-menu border rounded-md shadow-sm p-1 flex items-center space-x-1 bg-white"
  >
    <Popover>
      <PopoverTrigger class="flex items-center space-x-2">
        <Toggle v-bind="bubbleToggleConfig" :pressed="true">
          <IconLucideComponent
            :name="getTriggerContentTypography(editor)"
            v-bind="bubbleIconConfig"
          />
          <IconLucideComponent
            name="ChevronDown"
            class="w-3 h-3"
            :stroke-width="bubbleIconConfig.strokeWidth"
          />
        </Toggle>
      </PopoverTrigger>
      <PopoverContent v-bind="popoverContentHierarchyConfig">
        <div class="space-y-3">
          <div v-bind="labelInsidePopoverContentConfig">Hierarchy</div>
          <div class="space-y-1 flex flex-col">
            <Toggle
              :pressed="editor.isActive('paragraph')"
              @click="editor.chain().focus().setParagraph().run()"
              v-bind="bubbleToggleInsidePopoverContentConfig"
            >
              <IconLucideComponent name="Pilcrow" class="mr-1" v-bind="bubbleIconConfig" />
              Paragraph
            </Toggle>
            <Toggle
              v-for="level in levels"
              :key="level"
              :pressed="editor.isActive('heading', { level })"
              @click="editor.chain().focus().toggleHeading({ level }).run()"
              v-bind="bubbleToggleInsidePopoverContentConfig"
            >
              <IconLucideComponent
                :name="`Heading${level}` as keyof typeof icons"
                class="mr-1"
                v-bind="bubbleIconConfig"
              />
              Heading {{ level }}
            </Toggle>
          </div>
        </div>
        <div class="space-y-2">
          <div v-bind="labelInsidePopoverContentConfig">LISTS</div>
          <div class="space-y-1 flex flex-col">
            <Toggle
              :pressed="editor.isActive('bulletList')"
              @click="editor.chain().focus().toggleBulletList().run()"
              v-bind="bubbleToggleInsidePopoverContentConfig"
            >
              <IconLucideComponent name="List" class="mr-1" v-bind="bubbleIconConfig" />
              Bullet List
            </Toggle>
            <Toggle
              :pressed="editor.isActive('orderedList')"
              @click="editor.chain().focus().toggleOrderedList().run()"
              v-bind="bubbleToggleInsidePopoverContentConfig"
            >
              <IconLucideComponent name="ListOrdered" class="mr-1" v-bind="bubbleIconConfig" />
              Ordered List
            </Toggle>
          </div>
        </div>
      </PopoverContent>
    </Popover>
    <Toggle
      :pressed="editor.isActive('bold')"
      @click="editor.chain().focus().toggleBold().run()"
      :disabled="!editor.can().chain().focus().toggleBold().run()"
      v-bind="bubbleToggleConfig"
    >
      <IconLucideComponent name="Bold" v-bind="bubbleIconConfig" />
    </Toggle>
    <Toggle
      :pressed="editor.isActive('italic')"
      @click="editor.chain().focus().toggleItalic().run()"
      :disabled="!editor.can().chain().focus().toggleItalic().run()"
      v-bind="bubbleToggleConfig"
    >
      <IconLucideComponent name="Italic" v-bind="bubbleIconConfig" />
    </Toggle>
    <Toggle
      :pressed="editor.isActive('strike')"
      @click="editor.chain().focus().toggleStrike().run()"
      :disabled="!editor.can().chain().focus().toggleStrike().run()"
      v-bind="bubbleToggleConfig"
    >
      <IconLucideComponent name="Strikethrough" v-bind="bubbleIconConfig" />
    </Toggle>
    <Toggle
      :pressed="editor.isActive('code')"
      @click="editor.chain().focus().toggleCode().run()"
      :disabled="!editor.can().chain().focus().toggleCode().run()"
      v-bind="bubbleToggleConfig"
    >
      <IconLucideComponent name="Code" v-bind="bubbleIconConfig" />
    </Toggle>
    <Toggle
      :pressed="editor.isActive('codeBlock')"
      @click="editor.chain().focus().toggleCodeBlock().run()"
      :disabled="!editor.can().chain().focus().toggleCodeBlock().run()"
      v-bind="bubbleToggleConfig"
    >
      <IconLucideComponent name="CodeXml" v-bind="bubbleIconConfig" />
    </Toggle>
    <Popover>
      <PopoverTrigger>
        <Toggle v-bind="bubbleToggleConfig" :pressed="editor.isActive('link')">
          <IconLucideComponent name="Link2" v-bind="bubbleIconConfig" />
        </Toggle>
      </PopoverTrigger>
      <PopoverContent v-bind="popoverContentLinkConfig">
        <div class="space-y-2">
          <div v-bind="labelInsidePopoverContentConfig">Link</div>
          <form class="flex flex-col space-y-3" @submit.prevent="setLink">
            <div class="flex space-x-1">
              <div class="relative w-full max-w-sm items-center">
                <Input
                  placeholder="https://"
                  name="link"
                  class="h-9"
                  :default-value="editor.getAttributes('link').href"
                />
                <span
                  class="absolute end-0 inset-y-0 flex items-center justify-center px-2 cursor-pointer"
                >
                  <IconLucideComponent
                    @click="handleViewLink(editor.getAttributes('link').href)"
                    v-if="editor.isActive('link')"
                    name="Eye"
                    class="w-4 h-4 text-muted-foreground"
                  />
                </span>
              </div>
              <Button type="submit" size="sm">
                <IconLucideComponent name="Link2" v-bind="bubbleIconConfig" />
              </Button>
              <Button
                size="sm"
                type="button"
                v-if="editor.isActive('link')"
                @click="editor.chain().focus().unsetLink().run()"
                variant="destructive"
              >
                <IconLucideComponent name="Link2Off" v-bind="bubbleIconConfig" />
              </Button>
            </div>
            <div class="space-y-1" v-bind="labelInsidePopoverContentConfig">
              <p>- https://, http://, www. : to link to an external resource.</p>
              <p>- mailto: : to link to an email address.</p>
              <p>- tel: : to link to a phone number.</p>
            </div>
          </form>
        </div>
      </PopoverContent>
    </Popover>
  </bubble-menu>
  <div class="border rounded-md">
    <editor-content :editor="editor" />
  </div>
</template>
