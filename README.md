# vue-wysiwyg-editor

Vue Wysiwyg Editor is simple Html Editor with support for tags.

## Npm

npm install vue-wysiwyg-editor 

## Yarn
yarn add vue-wysiwyg-editor

### Example
```javascript
 import VueWysiwygEditor from 'vue-wysiwyg-editor';
  components: {
    HtmlEditor
  }
  
  <vue-wysiwyg-editor
      :tags="{0: '#name_of_tag_one', 1: '#name_of_tag_two', 2: '#name_of_tag_three'}"
      :text="name_of_your_vmodel"
      :text.sync="name_of_your_vmodel"
  ></vue-wysiwyg-editor>
```
       
### Callback
If you need callback for save text in editor use debounce.

```javascript
 <vue-wysiwyg-editor
      :tags="{0: '#name_of_tag_one', 1: '#name_of_tag_two', 2: '#name_of_tag_three'}"
      :text="name_of_your_vmodel"
      :text.sync="name_of_your_vmodel"
      v-on:debounce-input="debounceInputTextArea"
  ></vue-wysiwyg-editor>
  
  methods: {
    /*
    * Quando para de digitar um textarea
    */
    debounceInputTextArea: _.debounce(function (e)
    {
        // call your function where
    }, 500),  
  }
```
