<script setup lang="ts">
import { computed, watch, ref } from 'vue'

const symbols = [
  {
    name: "apple",
    image:
      "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMiAzMiI+PHBhdGggZmlsbD0iI2U3NGMzYyIgZD0iTTIzLDI3bC0yLDBsLTIsLTJsMCwwbC0zLDJsLTMsLTJsMCwwbC0yLDJsLTIsMGMtMSwwLTIsMC0zLTFjLTEsMC0xLTEtMS0yYzAsMCwwLDAsMCwwYzAsMCwwLTEsMC0xYzAsLTEsMCwtMiwwLC0zYzAsMCwwLC0xLDAsLTFjMCwtMiwxLC00LDIsLTVjMSwtMSwyLC0yLDQsLTNjMCwwLDAsMCwwLDBjMSwtMSwyLC0xLDMsLTFjMCwwLDAsMCwwLDBjMCwwLDAsMCwxLDBjMCwwLDEsMCwxLDBjMCwwLDEsMCwxLDBjMSwwLDEsMCwyLDBjMCwwLDAsMCwwLDBjMSwwLDIsMCwzLDFjMCwwLDAsMCwwLDBjMiwxLDMsMiw0LDNjMSwxLDIsMiwyLDVjMCwwLDAsMSwwLDFjMCwxLDAsMiwwLDNjMCwwLDAsMSwwLDFjMCwwLDAsMCwwLDBjMCwxLDAsMiwwLDJjLTEsMS0yLDEtMywxeiIvPjxwYXRoIGZpbGw9IiMzNDk4ZGIiIGQ9Ik0xNiw4YzAsMC0xLDAtMS0xYzAsLTEsMCwtMSwwLC0yYzAsLTEsMS0yLDItM2MxLC0xLDIsLTEsMiwtMmMwLDAsLTEsMC0xLDBjLTEsMC0yLDAtMywwYzAsLTEsMS0xLDEsLTFjMSwwLDIsMCwzLDBjMSwwLDIsMSwzLDFjMCwxLDAsMSwtMSwyYy0xLDAtMiwxLTIsMmMtMSwxLTEsMS0xLDJjMCwxLDAsMSwwLDJjMCwxLC0xLDEsLTEsMWMwLDAsMCwwLC0xLDB6Ii8+PC9zdmc+",
  },
  {
    name: "banana",
    image:
      "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMiAzMiI+PHBhdGggZmlsbD0iI2YxYzQwZiIgZD0iTTI4LDhjLTEsMC0xLDAtMiwwYy0xLDAtMiwwLTIsMGMtMSwwLTEsMC0yLDBjLTEsMC0yLDAtMiwwYy0xLDAtMiwwLTMsMWMtMSwwLTIsMC0zLDFjLTEsMC0yLDEtMiwxYy0xLDAtMiwxLTMsMmMtMSwxLTEsMS0yLDJjLTEsMS0xLDItMiwzYy0xLDEsMCwyLDAsMmMwLDEsMCwxLDEsMmMwLDAsMS0xLDEsLTFjMCwwLDEsLTEsMS0xYzEsLTEsMSwtMSwyLC0yYzEsMCwxLC0xLDIsLTFjMSwwLDEsLTEsMiwwYzAsMCwxLDAsMSwwYzEsMCwxLDAsMiwwYzEsMCwxLDAsMiwwYzEsMCwxLDAsMiwwYzEsMCwxLDAsMiwwYzEsMCwxLDAsMiwwYzAsMCwxLDAsMSwwYzEsMCwxLDAsMSwwYzAsLTEsMCwtMSwwLC0yYzAsLTEsMCwtMSwwLC0yYzAsLTEsMCwtMSwwLC0yYzAsLTEsMCwtMiwwLC0yYzAsLTEsMCwtMSwwLC0yYzAsLTEsMCwtMiwwLC0yeiIvPjxwYXRoIGZpbGw9IiM4ZTQ0YWQiIGQ9Ik0yOCw0YzEsMiwwLDIsMCwzYy0xLDAtMSwxLTEsMWMwLDEsLTEsMCwtMSwwYzAsMCwwLDAsLTEsMGMwLDAsMCwwLTEsMGMtMSwwLTEsMC0xLDBjLTEsMC0xLDAtMSwwYzAsMCwwLDAsMSwwYzEsMCwxLDAsMSwwYzEsMCwxLDAsMSwwYzEsMCwxLDAsMS0xYzAsLTEsMS0xLDEsLTFjMCwtMSwwLC0xLDAsLTJ6Ii8+PC9zdmc+",
  },
  {
    name: "cherry",
    image:
      "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMiAzMiI+PHBhdGggZmlsbD0iI2U3NGMzYyIgZD0iTTExLDI2Yy0zLDAtNSwtMi01LC01YzAsLTMsMiwtNSw1LC01YzMsMCw1LDIsNSw1YzAsMy0yLDUtNSw1eiIvPjxwYXRoIGZpbGw9IiNlNzRjM2MiIGQ9Ik0yMiwyN2MtMywwLTUsLTItNSwtNWMwLC0zLDIsLTUsNSwtNWMzLDAsNSwyLDUsNWMwLDMtMiw1LTUsNXoiLz48cGF0aCBmaWxsPSIjMmVjYzcxIiBkPSJNMTEsMTZjMCwwLDAsLTEsMCwtMWMwLC0xLDAsLTEsMCwtMmMwLDAsLTEsLTMsLTEsLTNjLTEsLTIsMCwtNCwyLC01YzAsMCwyLC0xLDIsLTFjMCwtMSwwLC0xLDAsLTFjMCwtMSwxLC0yLDIsLTJjMCwwLDYsLTEsNiwtMWMxLDEsMCwyLDAsMmMwLDEsLTEsMSwtMSwxYy0xLDAsLTIsMCwtMywwYy0xLDAtMSwxLTEsMWMtMSwwLTEsMC0xLDFjLTEsMC0xLDEtMSwyYzAsMC0xLDAsLTEsMWMwLDAsLTEsMSwtMSwxYzAsMSwwLDEsMCwxYzAsMSwwLDEsMCwxYy0xLDEsLTEsMS0xLDFjMCwxLC0xLDEsLTEsMnoiLz48cGF0aCBmaWxsPSIjMmVjYzcxIiBkPSJNMjIsMTdjMCwtMSwtMSwtMSwtMSwtMWMwLC0xLDAsLTEsMCwtMWMwLDAsLTEsLTMsLTEsLTNjLTEsLTEsMCwtMywyLC00YzEsLTEsMywtMiwzLC0yYzAsLTEsMCwtMSwwLC0xYzAsLTEsMSwtMiwyLC0yYzAsMCw1LC0xLDUsLTFjMSwxLDAsMiwwLDJjMCwwLC0xLDAsLTEsMWMtMSwwLTIsMCwtMywwYy0xLDAtMSwwLTEsMWMtMSwwLTEsMC0xLDFjLTEsMC0xLDEtMSwyYzAsMC0xLDAtMSwwYzAsMCwwLDEsLTEsMWMwLDEsMCwxLDAsMWMwLDEsMCwxLDAsMWMtMSwxLTEsMS0xLDFjMCwxLDAsMSwwLDF6Ii8+PC9zdmc+",
  },
  {
    name: "grapes",
    image:
      "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMiAzMiI+PHBhdGggZmlsbD0iIzhlNDRhZCIgZD0iTTksNmMwLDEsMCwxLDAsMWMwLDEsMCwxLDAsMmMwLDAsLTEsMSwtMSwxYy0xLDAtMiwxLTIsMWMtMSwxLTEsMi0xLDNjMCwxLDAsMiwxLDNjMCwwLDEsMSwyLDFjMSwwLDIsMCwzLDBjMSwwLDEsLTEsMiwtMWMxLDAsMS0xLDEsLTFjMCwtMSwwLC0yLDAsLTNjMCwtMSwwLC0xLDAsLTJjMCwwLDAsLTEsMCwtMWMwLDAsLTEsLTEsLTIsLTFjLTEsMC0yLDAtMywweiIvPjxwYXRoIGZpbGw9IiM4ZTQ0YWQiIGQ9Ik0xOCwxMGMwLTEsMC0xLDAtMWMwLC0xLDAsLTIsMCwtMmMwLDAsLTEsLTEsLTIsLTFjLTEsMC0yLDAtMywwYzAsMCwwLDAsMCwxYzAsMCwwLDEsMCwxYzAsMSwwLDEsMCwyYzAsMS0xLDEtMSwxYy0xLDEtMSwxLTEsMmMwLDEsMCwyLDAsMmMxLDEsMSwyLDIsMmMxLDEsMiwxLDMsMWMxLDAsMiwwLDItMWMxLDAsMi0xLDItMmMwLC0xLDAtMSwwLC0yYzAsLTEsMCwtMiwtMSwtMmMtMSwwLTEsLTEsLTEsLTF6Ii8+PHBhdGggZmlsbD0iIzhlNDRhZCIgZD0iTTI0LDE0YzEsMSwxLDEsMSwyYzEsMCwxLDEsMSwyYzAsMC0xLDEtMSwxYy0xLDEtMSwyLTIsMmMtMSwxLTIsMS0zLDFjLTEsMC0yLDAtMywtMWMtMSwwLTIsLTEsLTIsLTJjLTEsMC0xLC0xLTEsLTFjMCwtMSwwLC0yLDEsLTJjMCwtMSwwLC0xLDEsLTJjMCwwLDEsLTEsMS0xYzEsMCwyLDAsMywwYzEsMCwyLDEsMywxeiIvPjxwYXRoIGZpbGw9IiM4ZTQ0YWQiIGQ9Ik0xNCwxOGMwLDEsMSwxLDEsMWMwLDEsMCwxLDAsMmMwLDEsMCwxLTEsMmMwLDAtMSwxLTEsMWMtMSwwLTIsMC0zLDBjLTEsMC0xLC0xLTIsLTFjMCwtMSwtMSwtMSwtMSwtMmMwLC0xLDAsLTEsMCwtMmMwLDAsMS0xLDEsLTFjMSwwLDIsMCwzLDBjMSwwLDEsMCwzLDB6Ii8+PHBhdGggZmlsbD0iIzJlY2M3MSIgZD0iTTE1LDV2MGMxLDEsMiwwLDIsMGMwLC0xLDAsLTEsMSwtMWMwLDAsMSwwLDEsMGMwLDAsMSwtMSwxLC0xYzAsLTEsMCwtMSwxLC0xYzAsMCwwLDAsMCwwYzAsMC0xLDAtMSwwYy0xLDAtMSwwLTEsMGMtMSwwLTIsMC0yLDFjLTEsMC0xLDAtMiwxeiIvPjwvc3ZnPg==",
  },
  {
    name: "orange",
    image:
      "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMiAzMiI+PHBhdGggZmlsbD0iI2YzOWMxMiIgZD0iTTE2LDI3Yy02LDAtMTEsLTUtMTEsLTExYzAsLTYsNSwtMTEsMTEsLTExYzYsMCwxMSw1LDExLDExYzAsNi01LDExLTExLDExeiIvPjxwYXRoIGZpbGw9IiMyZWNjNzEiIGQ9Ik0xNiwxMmMwLDAtMSwwLTEsMGMwLDAsLTEsMC0xLDBjMCwtMSwwLC0xLDAsLTFjMCwtMSwwLC0xLDAsLTFjMCwtMSwwLC0xLDAsLTJjMCwwLDAsLTEsMCwtMWMwLDAsMS0xLDEsLTFjMCwwLDEsMCwxLDBjMCwwLDEsMCwxLDBjMCwwLDAsMCwxLDBjMCwwLDAsMSwwLDFjMCwwLDAsMSwwLDFjMCwxLDAsMSwwLDJjMCwwLDAsMSwwLDFjMCwwLDAsMSwwLDFjMCwwLC0xLDAsLTEsMGMwLDAsMCwwLC0xLDB6Ii8+PC9zdmc+",
  },
  {
    name: "pear",
    image:
      "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMiAzMiI+PHBhdGggZmlsbD0iIzJlY2M3MSIgZD0iTTE5LDI3Yy0xLDAtMiwwLTMsMGMtMSwwLTIsMC0zLDBjLTQsLTEsLTctMy03LC03YzAsLTEsMC0yLDAsLTJjMCwtMiwxLC00LDEsLTZjMCwtMSwxLC0zLDIsLTNjMSwtMSwyLC0xLDMsLTFjMSwwLDIsMCwzLDBjMSwwLDIsLTEsMiwtMWMxLDAsMSwwLDEsMGMxLDAsMSwxLDEsMWMwLDEsMCwyLDAsMmMwLDIsMCwzLDAsMGMwLDEsLTEsMywwLDJjMCwxLDEsMCwxLDJjMCwyLDAsMywxLDRjMSwyLDEsNCwxLDZjMCwxLDAsMywtMSw0Yy0xLDEtMiwxLTIsMXoiLz48cGF0aCBmaWxsPSIjZjM5YzEyIiBkPSJNMTYsOWMwLDAsMCwwLDEsMGMwLDAsMCwwLDEsMGMwLDAsMCwwLDEsMGMwLDAsMCwwLDAsMGMwLC0xLDAsLTEsMCwtMWMwLDAsLTEsLTEsLTEsLTFjMCwwLDAsLTEsLTEsLTFjMCwwLDAsMCwtMSwwYzAsMCwwLDAsMCwwYzAsMC0xLDEsLTEsMWMwLDAsMCwxLDAsMS0xLDAsLTEsMC0xLDF6Ii8+PC9zdmc+",
  },
  {
    name: "pineapple",
    image:
      "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMiAzMiI+PHBhdGggZmlsbD0iI2YzOWMxMiIgZD0iTTE2LDI2Yy0xLDAtMywwLTQsLTFjLTEsLTEsLTIsLTIsLTMsLTNjLTEsLTEsLTEsLTMsLTEsLTRjMCwtMiwwLC00LDAsLTVjMCwtMSwwLC0zLDEsLTRjMSwtMSwyLC0yLDMsLTNjMSwtMSwzLC0xLDQsLTFjMSwwLDMsMCw0LDFjMSwxLDIsMiwzLDNjMSwxLDEsMyAxLDRjMCwxLDAsMywwLDVjMCwxLDAsMy0xLDRjLTEsMS0yLDItMywzYy0xLDEsLTMsMS00LDF6Ii8+PHBhdGggZmlsbD0iIzJlY2M3MSIgZD0iTTE2LDVjLTEsMC0yLDAtMiwwYy0xLDAsLTEsLTEsLTEsLTFjMCwwLDAsLTEsLTEsLTFjMCwwLDAsMCwwLC0xYzAsMCwxLDAsMSwwYzEsMCwxLDAsMSwwYzEsMSwyLDAsMiwwYzAsMCwxLDAsMSwwYzAsMCwwLDAsMCwxYy0xLDAsLTEsMS0xLDFjMCwwLDAsMSwwLDFjMCwwLC0xLDAsLTEsMHoiLz48cGF0aCBmaWxsPSIjMzQ5OGRiIiBkPSJNMTcsMTRjMSwwLDEsMCwxLDBjMCwxLDAsMSwxLDFjMCwwLDEsMCwxLDBjMCwxLDAsMS0xLDFjMCwwLDAsMCwwLDFjMCwwLDAsMC0xLDBjMCwwLDAsLTEsLTEsLTFjMCwwLDAsMC0xLDB6Ii8+PHBhdGggZmlsbD0iIzM0OThkYiIgZD0iTTEzLDE0YzAsMCwwLDAsMCwwYzAsMSwwLDEsLTEsMWMwLDAsMCwwLTEsMGMwLDEsMCwxLDAsMWMwLDAsMSwwLDEsMWMwLDAsMSwwLDEsMGMwLDAsMCwwLDAsLTFjMCwwLDEsLTEsMS0xeiIvPjxwYXRoIGZpbGw9IiMzNDk4ZGIiIGQ9Ik0xMywxOWMwLDAsMCwwLDAsMGMwLDAsMCwwLC0xLDBjMCwwLDAsMCwtMSwwYzAsMCwwLDEsMCwxYzAsMCwxLDAsMS4wNSwwYzAsMCwwLDAsMSwwYzAsMCwwLTEsMCwtMWMwLDAsMC0xLDAtMXoiLz48cGF0aCBmaWxsPSIjMzQ5OGRiIiBkPSJNMTcsMTljMCwwLDAsMCwwLDBjMCwwLDEsMCwxLDBjMCwwLDEsMCwxLDBjMCwwLDAsMSwwLDFjMCwwLC0xLDAsLTEsMGMwLDAsMCwwLC0xLDBjMCwwLDAsLTEsMCwtMWMwLDAsMC0xLDAsLTF6Ii8+PC9zdmc+",
  },
  {
    name: "strawberry",
    image:
      "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMiAzMiI+PHBhdGggZmlsbD0iI2U3NGMzYyIgZD0iTTE2LDI3Yy02LDAtMTAsLTUtMTAsLTZjMCwtMSwwLC0yLDAsLTNjMCwtMSwwLC0yLDAsLTNjMCwtMSwwLC0yLDAsLTJjMCwtMSwwLC0xLDAsLTJjMCwtMSwxLC0xLDEsLTJjMCwwLDEsLTIsMiwtMmMxLC0xLDIsLTEsMiwtMWMxLDAsMiwwLDMsMGMxLDAsMiwwLDMsMGMwLDAsMSwwLDIsMWMxLDAsMiwxLDIsMmMwLDEsMSwxLDEsMmMwLDEsMCwxLDAsMmMwLDAsMCwxLDAsMmMwLDEsMCwyLDAsMmMwLDEsMCwzLDAsMmMwLDEsLTQsNywtMTAsN3oiLz48cGF0aCBmaWxsPSIjZmZmZmZmIiBkPSJNMTQsMTFjMCwxLDEsMSwxLDFjMCwwLDEsMCwxLC0xYzAsMCwwLDAsLTEsMGMwLDAsLTEsMCwtMSwweiIvPjxwYXRoIGZpbGw9IiNmZmZmZmYiIGQ9Ik0xOSwxM2MwLDAsMC0xLDAsLTFjMCwwLDAsMC0xLDBjMCwwLDAsMCwwLDFjMCwwLDEsMCwxLDB6Ii8+PHBhdGggZmlsbD0iI2ZmZmZmZiIgZD0iTTIxLDE3YzAsMCwwLC0xLDAsLTFjMCwwLDAsMCwtMSwwYzAsMCwwLDAsMCwxYzAsMCwxLDAsMSwweiIvPjxwYXRoIGZpbGw9IiNmZmZmZmYiIGQ9Ik0xMywxOGMwLDAsMCwtMSwwLC0xYzAsMCwwLDAsLTEsMGMwLDAsMCwwLDAsLTFjMCwwLDAsMCwtMSwwYzAsMSwwLDEsMCwxYzAsMCwxLDAsMSwweiIvPjxwYXRoIGZpbGw9IiNmZmZmZmYiIGQ9Ik0xNSwxNmMwLDAsMCwtMSwwLC0xYzAsMCwwLDAsLTEsMGMwLDAsMCwwLDAsMWMwLDAsMSwwLDEsMHoiLz48cGF0aCBmaWxsPSIjZmZmZmZmIiBkPSJNMTcsMTljMCwwLDAsLTEsMCwtMWMwLDAsMCwwLC0xLDBjMCwwLDAsMCwwLDFjMCwwLDEsMCwxLDB6Ii8+PHBhdGggZmlsbD0iI2ZmZmZmZiIgZD0iTTExLDE1YzAsMCwwLC0xLDAsLTFjMCwwLDAsMCwtMSwwYzAsMCwwLDAsMCwxYzAsMCwxLDAsMSwweiIvPjxwYXRoIGZpbGw9IiMyZWNjNzEiIGQ9Ik0xNiw2YzAsMCwwLDAsLTEsMGMwLDAsLTEsMCwtMSwwYzAsMCwwLDAsMCwtMWMwLDAsLTEsLTEsLTEsLTFjMCwwLDAsLTEsMSwtMWMwLDAsMCwwLDEsMGMwLDAsMSwwLDEsMGMxLDAsMSwxLDEsMWMwLDAsMCwxLDAsMWMwLDAsMCwxLC0xLDF6Ii8+PC9zdmc+",
  },
  {
    name: "watermelon",
    image:
      "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMiAzMiI+PHBhdGggZmlsbD0iIzJlY2M3MSIgZD0iTTI3LDIyYy0xLDEtMSwxLTIsMmMtMSwxLTMsMi01LDJjLTIsMC0zLDAtNSwwYy0yLDAtNCwtMS01LC0yYy0xLC0xLTIsLTEtMiwtMmMtMSwtMSwtMiwtMywtMiwtNGMtMSwtMiwwLC0zLDAsLTVjMCwtMiwxLC00LDIsLTVjMCwtMSwxLC0yLDIsLTJjMSwtMSwzLC0yLDUsLTJjMiwwLDMsMCw1LDBjMiwwLDQsMSw1LDJjMSwwLDIsMSwyLDJjMSwxLDIsMywyLDVjMCwyLDEsMyxwLDVjMCwxLC0xLDMsLTIsMXoiLz48cGF0aCBmaWxsPSIjZTc0YzNjIiBkPSJNMjYsMjBjLTEsMC0yLDEtMywxYy0xLDEtMiwxLTQsMWMtMSwwLTIsMC0zLDBjLTIsMC0zLDAtNCwtMWMtMSwwLTIsLTEsLTMsLTFjLTEsLTEsLTEsLTIsLTIsLTNjMCwtMSwwLC0yLDAsLTNjMCwtMSwxLC0yLDIsLTNjMCwtMSwyLC0xLDMsLTFjMSwtMSwyLC0xLDQsLTFjMSwwLDIsMCwzLDBjMiwwLDMsMCw0LDFjMSwwLDIsMCwzLDFjMSwxLDEsMiwyLDNjMCwxLDAsMiwwLDNjMCwxLC0xLDIsLTIsM3oiLz48cGF0aCBmaWxsPSIjMzQ5OGRiIiBkPSJNMTIsMTNjMCwwLDEsMCwxLDBjMCwwLDAsMSwwLDFjMCwwLC0xLDAsLTEsMGMwLDAsMCwtMSwwLC0xeiIvPjxwYXRoIGZpbGw9IiMzNDk4ZGIiIGQ9Ik0xOCwxM2MwLDAsMSwwLDEsMGMwLDAsMCwxLDAsMWMwLDAsLTEsMCwtMSwwYzAsMCwwLC0xLDAsLTF6Ii8+PHBhdGggZmlsbD0iIzM0OThkYiIgZD0iTTIyLDE1YzAsMCwxLDAsMSwwYzAsMCwwLDEsMCwxYzAsMCwtMSwwLC0xLDBjMCwwLDAsLTEsMCwtMXoiLz48cGF0aCBmaWxsPSIjMzQ5OGRiIiBkPSJNMjAsMThjMCwwLDEsMCwxLDBjMCwwLDAsMSwwLDFjMCwwLC0xLDAsLTEsMGMwLDAsMCwtMSwwLC0xeiIvPjxwYXRoIGZpbGw9IiMzNDk4ZGIiIGQ9Ik0xNiwxOGMwLDAsMSwwLDEsMGMwLDAsMCwxLDAsMWMwLDAsLTEsMCwtMSwwYzAsMCwwLC0xLDAsLTF6Ii8+PHBhdGggZmlsbD0iIzM0OThkYiIgZD0iTTEyLDE4YzAsMCwxLDAsMSwwYzAsMCwwLDEsMCwxYzAsMCwtMSwwLC0xLDBjMCwwLDAsLTEsMCwtMXoiLz48cGF0aCBmaWxsPSIjMzQ5OGRiIiBkPSJNMTAsMTVjMCwwLDEsMCwxLDBjMCwwLDAsMSwwLDFjMCwwLC0xLDAsLTEsMGMwLDAsMCwtMSwwLC0xeiIvPjxwYXRoIGZpbGw9IiMzNDk4ZGIiIGQ9Ik0xNSwxNWMwLDAsMSwwLDEsMGMwLDAsMCwxLDAsMWMwLDAsLTEsMCwtMSwwYzAsMCwwLC0xLDAsLTF6Ii8+PHBhdGggZmlsbD0iIzM0OThkYiIgZD0iTTE0LDIxYzAsMCwxLDAsMSwwYzAsMCwwLDEsMCwxYzAsMCwtMSwwLC0xLDBjMCwwLDAsLTEsMCwtMXoiLz48cGF0aCBmaWxsPSIjMzQ5OGRiIiBkPSJNMTgsMjFjMCwwLDEsMCwxLDBjMCwwLDAsMSwwLDFjMCwwLC0xLDAsLTEsMGMwLDAsMCwtMSwwLC0xeiIvPjwvc3ZnPg==",
  },
];

// Variables
const cards = ref([]);
const firstCard = ref(null);
const secondCard = ref(null);
const disableCards = ref(false);
const moves = ref(0);
const matches = ref(0);
const totalPairs = ref(symbols.length);
const isGameWon = computed(() => matches.value === totalPairs.value);

// Initialize game
const initializeGame = () => {
  // Create pairs of cards
  const cardPairs = [...symbols, ...symbols].map((symbol, index) => ({
    id: index,
    name: symbol.name,
    image: symbol.image,
    flipped: false,
    matched: false,
  }));

  // Shuffle cards
  cards.value = shuffleCards(cardPairs);
};

// Shuffle cards using Fisher-Yates algorithm
const shuffleCards = (array) => {
  const result = [...array];
  for (let i = result.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [result[i], result[j]] = [result[j], result[i]];
  }
  return result;
};

// Handle card flip
const flipCard = (index) => {
  const card = cards.value[index];

  // Prevent flipping already matched or flipped cards
  if (card.flipped || card.matched) return;

  // Flip the card
  card.flipped = true;

  // Logic for checking matches
  if (firstCard.value === null) {
    // First card flipped
    firstCard.value = index;
  } else if (secondCard.value === null && index !== firstCard.value) {
    // Second card flipped
    secondCard.value = index;
    moves.value++;

    // Check for match
    checkForMatch();
  }
};

// Check if two flipped cards match
const checkForMatch = () => {
  const card1 = cards.value[firstCard.value];
  const card2 = cards.value[secondCard.value];

  if (card1.name === card2.name) {
    // Cards match
    card1.matched = true;
    card2.matched = true;
    matches.value++;
    resetSelection();
  } else {
    // Cards don't match - flip them back
    disableCards.value = true;
    setTimeout(() => {
      card1.flipped = false;
      card2.flipped = false;
      resetSelection();
    }, 1000);
  }
};

// Reset card selection
const resetSelection = () => {
  firstCard.value = null;
  secondCard.value = null;
  disableCards.value = false;
};

// Reset game
const resetGame = () => {
  moves.value = 0;
  matches.value = 0;
  firstCard.value = null;
  secondCard.value = null;
  disableCards.value = false;

  // Reset all cards
  cards.value.forEach((card) => {
    card.flipped = false;
    card.matched = false;
  });

  // Re-shuffle cards
  setTimeout(() => {
    cards.value = shuffleCards(cards.value);
  }, 300);
};

// Initialize game on component creation
initializeGame();
</script>

<template>
  <div id="app">
    <h1>Memory Game</h1>

    <div class="game-info">
      <div>Moves: {{ moves }}</div>
      <div>Matches: {{ matches }} / {{ totalPairs }}</div>
    </div>

    <div class="board">
      <div
        v-for="(card, index) in cards"
        :key="index"
        class="card"
        :class="{ flipped: card.flipped, matched: card.matched }"
        @click="flipCard(index)"
        :style="{ pointerEvents: disableCards ? 'none' : 'auto' }"
      >
        <div class="card-inner">
          <div class="card-face card-back"></div>
          <div class="card-face card-front">
            <img :src="card.image" :alt="card.name" />
          </div>
        </div>
      </div>
    </div>

    <button @click="resetGame">New Game</button>

    <div v-if="isGameWon" class="win-message">
      Congratulations! You won in {{ moves }} moves!
    </div>
  </div>
</template>

<style>
* {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
      }

      body {
        font-family: "Arial", sans-serif;
        background-color: #f4f7f9;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        padding: 20px;
      }

      #app {
        width: 100%;
        max-width: 800px;
        text-align: center;
      }

      h1 {
        color: #2c3e50;
        margin-bottom: 20px;
      }

      .game-info {
        margin-bottom: 20px;
        display: flex;
        justify-content: space-between;
        padding: 0 10px;
      }

      .game-info div {
        font-size: 1.2rem;
        font-weight: bold;
        color: #34495e;
      }

      .board {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        grid-gap: 15px;
        margin: 0 auto;
      }

      .card {
        height: 120px;
        perspective: 1000px;
        cursor: pointer;
      }

      .card-inner {
        position: relative;
        width: 100%;
        height: 100%;
        transform-style: preserve-3d;
        transition: transform 0.6s;
      }

      .card.flipped .card-inner {
        transform: rotateY(180deg);
      }

      .card-face {
        position: absolute;
        width: 100%;
        height: 100%;
        backface-visibility: hidden;
        display: flex;
        justify-content: center;
        align-items: center;
        border-radius: 8px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
      }

      .card-back {
        background-color: #3498db;
        border: 2px solid #2980b9;
      }

      .card-front {
        background-color: #fff;
        border: 2px solid #e0e0e0;
        transform: rotateY(180deg);
      }

      .card-front img {
        width: 60%;
        height: 60%;
        object-fit: contain;
      }

      .card.matched .card-front {
        background-color: #e6ffe6;
        border-color: #2ecc71;
      }

      button {
        margin-top: 20px;
        padding: 10px 20px;
        background-color: #3498db;
        color: white;
        border: none;
        border-radius: 4px;
        font-size: 1rem;
        cursor: pointer;
        transition: background-color 0.3s;
      }

      button:hover {
        background-color: #2980b9;
      }

      .win-message {
        margin-top: 20px;
        font-size: 1.5rem;
        color: #27ae60;
        font-weight: bold;
      }

      @media (max-width: 600px) {
        .board {
          grid-template-columns: repeat(3, 1fr);
        }
        .card {
          height: 100px;
        }
      }
</style>