<template>
    <div id="html-editor">
        <div class="toolbar btn-toolbar" role="toolbar" aria-label="...">
            <div class=" btn-group" role="toolbar">
                <button type="button" class="btn btn-default" v-on:click="addCommand('bold')">
                    <i class="fa fa-bold"></i>
                </button>

                <button type="button" class="btn btn-default" v-on:click="addCommand('italic')">
                    <i class="fa fa-italic"></i>
                </button>

                <button type="button" class="btn btn-default" v-on:click="addCommand('underline')">
                    <i class="fa fa-underline"></i>
                </button>
            </div>

            <div class=" btn-group" role="toolbar">
                <button type="button" class="btn btn-default" v-on:click="addCommand('justifyCenter')">
                    <i class="fa fa-align-center"></i>
                </button>

                <button type="button" class="btn btn-default" v-on:click="addCommand('justifyLeft')">
                    <i class="fa fa-align-left"></i>
                </button>

                <button type="button" class="btn btn-default" v-on:click="addCommand('justifyRight')">
                    <i class="fa fa-align-right"></i>
                </button>

                <button type="button" class="btn btn-default" v-on:click="addCommand('justifyFull')">
                    <i class="fa fa-align-justify"></i>
                </button>
            </div>

            <div class=" btn-group" role="toolbar">
                <button type="button" class="btn btn-default" v-on:click="addCommand('insertUnorderedList')">
                    <i class="fa fa-list-ul"></i>
                </button>

                <button type="button" class="btn btn-default" v-on:click="addCommand('insertOrderedList')">
                    <i class="fa fa-list-ol"></i>
                </button>
            </div>
        </div>

        <div class="editor" ref="text" contenteditable="true" v-html="text" v-once v-on:input="debounceInput" @input="sync($event.target.innerHTML)"></div>

        <div class="content-tags" v-if="tags">
            <ul class="list-inline">
                <li v-for="tag in tags"><button type="button" class="btn-tag" v-on:click="addTag(tag)">{{tag}}</button></li>
            </ul>
        </div>

    </div>
</template>

<script>
    export default {
        props: {
            text: {
                required: false
            },

            tags: {
                required: false
            }
        },

        watch: {
            text (text)
            {
                if(!text)
                {
                    this.$refs.text.innerHTML = '';
                }
                else if(!this.$refs.text.innerHTML)
                {
                    this.$refs.text.innerHTML = text;
                }
            }
        },

        mounted: function() {
            if(this.tags)
            {
                this.onBackSpace();
            }
        },

        methods: {

            debounceInput: _.debounce(function (e)
            {
                this.$emit('update:text', this.$refs.text.innerHTML);
                this.$emit('debounce-input', this.$refs.text.innerHTML);
            }, 500),

            addCommand: function(command)
            {
                document.execCommand(command, false, null);
            },

            sync: function(html) {
                this.$emit('update:text', html);
            },

            addTag: function(html)
            {
                this.$refs.text.focus();

                html = "<label contenteditable='false' class='tag'>"+html+"</label>&nbsp";

                var sel, range;
                if (window.getSelection) {

                    sel = window.getSelection();
                    if (sel.getRangeAt && sel.rangeCount) {
                        range = sel.getRangeAt(0);
                        range.deleteContents();

                        var el = document.createElement("div");
                        el.innerHTML = html;
                        var frag = document.createDocumentFragment(), node, lastNode;
                        while ( (node = el.firstChild) ) {
                            lastNode = frag.appendChild(node);
                        }

                        range.insertNode(frag);

                        if (lastNode) {
                            range = range.cloneRange();
                            range.setStartAfter(lastNode);
                            range.collapse(true);
                            sel.removeAllRanges();
                            sel.addRange(range);
                        }
                    }

                } else if (document.selection && document.selection.type != "Control") {
                    document.selection.createRange().pasteHTML(html);
                }

                this.debounceInput();
            },

            onBackSpace: function()
            {
                this.$refs.text.addEventListener('keydown', (event) =>{
                    // Check for a backspace
                    if (event.which == 8) {
                        var s = window.getSelection();
                        var r = s.getRangeAt(0)
                        var el = r.startContainer.parentElement
                        // Check if the current element is the .label
                        if (el.classList.contains('tag')) {
                            // Check if we are exactly at the end of the .label element
                            if (r.startOffset == r.endOffset && r.endOffset == el.textContent.length) {
                                // prevent the default delete behavior
                                event.preventDefault();
                                if (el.classList.contains('highlight')) {
                                    // remove the element
                                    el.remove();
                                } else {
                                    el.classList.add('highlight');
                                }

                                this.debounceInput();

                                return;
                            }
                        }
                    }
                    event.target.querySelectorAll('span.label.highlight').forEach(function(el) { el.classList.remove('highlight');})
                });
            }
        }
    }
</script>
<style type="text/css">

#html-editor .toolbar {
    right: 25px;
    position: absolute;
    top: 8px;
}

#html-editor .toolbar button {
    background-color: #fff;
}

.editor {
    padding: 25px 15px 15px 15px !important;
    min-height: 200px;
    overflow: auto;
    resize: vertical;
    outline: none;
    padding: 1em;
    border-radius: 3px;
    border: 1px solid #d5dce0;
    color: #151b26;
    -webkit-box-shadow: none;
    box-shadow: none;
    -webkit-transition: all 300ms linear;
    -moz-transition: all 300ms linear;
    -o-transition: all 300ms linear;
    -ms-transition: all 300ms linear;
    transition: all 300ms linear;
    line-height: 30px;
}

#html-editor .tag {
    font-size: 14px;
    line-height: 24px;
    color: #2696d3;
    background-color: #CFF0FC;
    border-radius: 3px;
    margin: 0;
    display: inline-block;
    padding-right: 4px;
    padding-left: 4px;
    margin-right: 1px;
    margin-left: 1px;
    padding: 5px;
    border-radius: 20px;
    padding-top: 2px;
    padding-bottom: 2px;
    border: 2px solid #2696d3;
}

#html-editor button.btn-tag {
    margin-top: 10px;
    background: #d0f1fc !important;
    border: none;
    padding: 5px;
    color: #3d9cd6;
    border-radius: 21px;
    border: 2px solid #3d9cd6;
    font-weight: 600;
}

</style>
