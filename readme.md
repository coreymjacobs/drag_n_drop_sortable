# Drag n Drop, sortable

Node.js file for native sortable drag n drop for DIVs that have nested children. Other scripts/modules were not working correctly with nested children so created this one.

Built using node v11.9.0

---

## Example Code:

---

### HTML

```html

<!-- index.html -->
<!-- create a container -->
<div class="container_data" id="data_container">
    
    <!-- create DIVs that you want sorted with nested children -->
    <div class="data_block" id="data_block_1">Block 1
      <span class="data_title" id="child_1">Child 1</span>
    </div>

    <div class="data_block" id="data_block_2">Block 2
      <span class="data_title" id="child_2">Child 2</span>
    </div>
    
    <div class="data_block" id="data_block_3">Block 3
      <span class="data_title" id="child_3">Child 3</span>
    </div>
    
    <div class="data_block" id="data_block_4">Block 4
      <span class="data_title" id="child_4">Child 4</span>
    </div>
    
    <div class="data_block" id="data_block_5">Block 5
      <span class="data_title" id="child_5">Child 5</span>
    </div>

  </div>

    <!-- include javascript file -->
    <script src="drag_drop.js"></script>
    <script>
    //pass in class name of DIVs that will be sortable, NOT the container class/id
    //in this case 'data_block'
    drag_drop('data_block');
    </script>

```

### CSS
```css

/* when the selected item is held */
.held {
    border: dashed 1px black;
    background-color: lightgray;
}

/* for the parent and child blocks */
.data_block {
    border: solid 1px black;
    width: 500px;
    padding: 20px;
}

.data_title {
    border: solid 1px black;
    width: 200px;
    padding: 2px;
}

```


### Javascript

```javascript
//javascript

async function drag_drop (parent_div) {
    //get all child elements, blocks
    //returns a htmlCollection
    const blocks = document.getElementsByClassName(parent_div);

    //set listeners and properties for all of the div blocks
    for (const block of blocks) {
        block.addEventListener("dragover", dragOver);
        block.addEventListener("dragstart", dragStart);
        block.addEventListener("dragend", dragEnd);
        block.addEventListener("dragenter", dragEnter);
        block.addEventListener("dragleave", dragLeave);
        block.addEventListener("drop", dragDrop);
        //set all with class 'data_block' as draggable
        block.draggable = true;
    }

    //global var for selected item
    let selected_block;

    function dragStart(e) {
        //set global variable for selected item being dragged
        //dateTranfser is not working for some reason
        selected_block = e.target.id;
        //change css
        this.classList.add('held');
    }

    function dragEnter(e) {
        //check if draggable, if not don't allow drop!!!
        //e.target is the item being hovered over
        //prevents child spans from being used
        e.target.getAttribute("draggable") ? e.dataTransfer.dropEffect = "move" : e.dataTransfer.dropEffect = "none";

        //find which block want to move to
        //loop through all items in blocks (parent div) and match which item in html collection
        //this allows for sorting
        //honestly not sure why or how this works, but need to match them before proceeding
        //just pulling the e.target.id throws an error
        for (let i = 0; i < blocks.length; ++i) {
            //need to match the selected and target for this to work so can manipulate htmlcollection
            //can't just use onDragStart value and target
            //need to do this everytime enter a new block
            if (blocks[i].id == e.target.id) {
                //will always match block selected first, so can set variable to compare if before or after
                //small pause
                setTimeout( () => {
                    //check if first item in collection
                    if (i == 0) {
                        //if first item, can't use next sibling
                        blocks[i].parentNode.insertBefore(document.getElementById(selected_block), blocks[i]);
                    }
                    else {
                        //all other cases and last item
                        //can use nextSibling
                        blocks[i].parentNode.insertBefore(document.getElementById(selected_block), blocks[i].nextSibling);
                    }
                },0);
                //stop the for loop
                break;
            }
        }
    }

    function dragOver(e) {
        e.preventDefault();
    }

    function dragLeave(e) {
        e.preventDefault();
    }

    function dragEnd(e) {
        this.classList.remove('held');
    }

    function dragDrop(e) {
        e.preventDefault();
    }
}

```
