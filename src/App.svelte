<script>

    import { onMount } from "svelte";

   let jsonData = [];
  let data = [];

 onMount(async () => {

        const response = await fetch(
"https://api.recruitly.io/api/candidate?apiKey=TEST9349C0221517DA4942E39B5DF18C68CDA154"

);
const responseData = await response.json();
jsonData = responseData.data;
console.log(jsonData, "json");
const gridData = jsonData.map(item => ({

   reference:item.reference,
   name: item.fullName,
   email:item.email,
  phone:item.mobile,


}));

console.log(gridData,"griddata");

    // });

 
 var dataGrid = new DevExpress.ui.dxDataGrid("#dataGrid", {

        dataSource: gridData,

        columns: [

        { dataField: 'reference', caption: 'ID' },

        { dataField: 'name', caption: 'Name', dataType: "url" },

        { dataField: 'email', caption: 'Email' },

        { dataField: 'phone', caption: 'Mobile' },

        // Define other columns as needed

      ],

 

        

        showBorders: true,

        filterRow: {

            visible: true,

        },
editing: {
allowDeleting: true,
allowAdding: true,
allowUpdating: true,
mode: "popup",
form: {
labelLocation: "top"
},
popup: {
showTitle: true,
title: "Row in the editing state"
}
},
 paging: {
pageSize: 10,

        },
pager: {

            showPageSizeSelector: true,

            allowedPageSizes: [5, 10, 20],

            showInfo: true,

        },

    });

    });

</script>

<div id="dataGrid"></div>