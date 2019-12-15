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

The code for this Workshop can be found [here](https://github.com/profimedica/Teaching.jQuery)

Create, Read, Update, Delete ([CRUD](http://google.com) https://en.wikipedia.org/wiki/Create,_read,_update_and_delete