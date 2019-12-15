Workshop 1: Creating a CRUD application

We will be using [jQuery](https://jquery.com) and [DataTables](https://datatables.net) to create this [CRUD](http://aju.ro/crud) application.

# Why jQuery?

Because is small and we can use it right away. Preparing your computer for an Angular solution will take considarably more time.

# Why DataTables?

DataTables is a powerful jQuery plugin for creating table listings and adding interactions to them. It provides searching, sorting and pagination without any configuration.

Zero configuration requires at least two files to be included.

    <script src="https://cdn.datatables.net/1.10.20/js/jquery.dataTables.min.js"></script>	 
    <link rel="stylesheet" href="https://cdn.datatables.net/1.10.20/css/jquery.dataTables.min.css">

We usually need a custom configuration. I found it usefull to set the initial dataSet to an empty array and fill the table later. With pageLength you can set the table length. Working with more than 100 rows might slow down your app responsivity.

    myTable = $('#table_bus_seats').DataTable({
        data: dataSet,
        rowId: "id",
        pageLength: 30,
        select: {
            style: 'os',
            className: 'focusedRow',
            selector: 'td:first-child'
        },
        "columns": [
            { "data": "firstName" },
            { "data": "secondName" }
        ]
    });

You can add render functions co create custom column editors. In this example I create a dropdown menu with commands:


    {
        data: "userId",
        render: function ( data, type, row ) { 
            return '<span class="dropdown"><a class="btn" href="#" role="button" id="dropdownMenuLink1" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false"><i class="fa fa-cog"></i></a><a class="dropdown-item" onclick="EditItem(\'bus\', ' + row.id + ')" href="#">Edit</a><a class="dropdown-item" onclick="RemoveItem(\'bus\', ' + row.id + ')" href="#">Delete</a></div></span>';
        }
    },

When the selection model is changed and you receive a new set of data, you want to update the table. If you need to filter the daat on client side this is a possible approach where I use the score to rank my objects:

    function FilterCustomersTable(model)
    {
        customersTable.clear();
        filtredCustomerItems = customerItems.filter(p=> {
            return sameTime(p.Date, model.minute)
        });
        filtredCustomerItems.forEach(row => { 
            row.score = rowIndex;
            customersTable.rows.add([row]);
        });
        customersTable.draw();
    }

## jQuery CRUD

Before actially editing or deleting objects we need to prepare them for the action. I assume we have two buttons on each row of wour table: Edit and Delete. We will attach an event to those buttons (actially to the class of each button type). Once the action button pressed, the selectedItem will point to the item you want to edit or delete.

    $('.delete').on( 'click', function () {
        var data = table.row( $(this).parents('tr') ).data();
        selectedItem = data;                             
        for(var propertyName in data)                       
        {                      
            setControlValue("delete", propertyName, data[propertyName]);                      
        }
        $('#modal_delete').modal('show');         
    });

The modal dialogue is used to confirm the action. We will be avoiding user errors and give the chance to user to cancel an unwanted action. The following code is not complete:

    <div class="modal fade" id="modal_delete">
      <div class="modal-header">
        <h5>Delete this Item ?</h5>
      </div>
      <div class="modal-body">
            <label for="input_Image">Name</label>
            <input type="text" disabled="true" id="input_Name">
      </div>
      <div class="modal-footer">
        <button type="button" onclick="deleteItem()" class="btn btn-primary">Yes</button>
        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
      </div>
    </div>

### Create



### Read

### Update

Once the object is selected for edit we can populate the edit form with the values. We can make things simple with this function that is walking through the object's properties and for each property is finding the equivalent controla and fill the value.

    function setControlValue(collection, propertyName, newValue){
        if(collection == 'view')
        {
            controls = $('#view_' + propertyName);
        }
        else
        {
            controls = $('#modal_' + collection + ' #input_' + propertyName);
        }
        if(controls.length > 0)
        {
        switch(controls[0].type)
        {
            default:
            if(propertyName == 'json')
            {
                var mdo = newValue['Peace Activists'];   
                if(mdo)
                {
                    mdo = mdo[mdo.length-1];
                    if(mdo.md)
                    {
                        updateColMd(mdo.md);
                    }
                }
                $(controls[0]).val(JSON.stringify(newValue));  
            }
            else
            {
                $(controls[0]).val(newValue);  
            }
            break;                      
        }                      
        }                      
    }

### Delete

Delete action is a POST request to a server we already have. The response will be a JSON object with the id of the deleted item. The deleted item will be removed from the table and the confirmation modal will be closed.

    function deleteItem(mode){
        $.post("?delete", selectedItem).done(function( data ) {
            if(data != null)
            {
                selectedItem = JSON.parse(data);
                if(selectedItem.id > 0)
                {
                    table.row('#' + selectedItem.id).remove().draw();                    
                    $('#modal_delete').modal('hide'); 
                    
                }
            };
        });
    } 


The code for this Workshop can be found [here](https://github.com/profimedica/Teaching.jQuery)

Create, Read, Update, Delete ([CRUD](http://google.com) https://en.wikipedia.org/wiki/Create,_read,_update_and_delete