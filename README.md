<?php
session_start();
if (!isset($_SESSION['loggedin'])) {
    header("Location: welcome.php");
    exit;
}

include 'link.php';

// Get the current user's permissions
$username = $_SESSION['username'];
$sql = "SELECT add_case FROM permissions WHERE username = '$username'";
$result = $conn->query($sql);
if ($result->num_rows > 0) {
    $row = $result->fetch_assoc();
    if ($row['add_case'] == 0) {
        // If the user does not have permission to view the technical page, redirect them
        header("Location: function.php");
        exit;
    }
} else {
    // If no user is found, redirect
    header("Location: welcome.php");
    exit;
}
?>

<!DOCTYPE html>
<html lang="en">
<head>

    <meta charset="UTF-8">
    <title>Service Form</title>
    
    <style>
  /* Additional CSS */
#PartTable input[name="price"],
#PartTable input[name="quantity"],
#PartTable input[name="total"],
#ItemTable input[name="qty"] {
    width: 100px; /* Adjust width as needed */
}
@media print {
    button, .logo {
        display: none;
    }
}


@media print {
    html, body {
        height: 100%;
        margin: 0;
        padding: 0;
        font-size: 17pt; /* Slightly smaller font size for print */
        color: #333;
    }
    .scaffold {
        width: 210mm; /* A4 width */
        height: 297mm; /* A4 height */
        padding: 3mm; /* Reduced padding for print */
        box-shadow: none; /* Remove shadow for print */
        overflow: visible; /* Ensure no overflow */
    }
    .topside {
        margin-bottom: 30px; /* Reduced spacing for header */
        border-bottom: 1px solid #000; /* Stronger border for print */
    }
    .form-group {
        margin-bottom: 8px; /* Reduced spacing for fields */
    }
    .line {
        margin: 10px 0; /* Adjusted margin between sections */
    }
    table {
        width: 100%;
        border-collapse: collapse; /* Collapse borders for cleaner look */
        font-size: 10pt; /* Smaller font for table */
    }
    th, td {
        padding: 4px; /* Reduced padding for cells */
        text-align: left; /* Align text to left for readability */
        border: 1px solid #000; /* Solid border for tables */
    }
    th {
        background-color: #f2f2f2; /* Light background for headers */
        font-weight: bold;
    }
    .action-table {
        display: none; /* Hide unnecessary elements */
    }
}



.topside {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 1px solid #e0e0e0;
    padding-bottom: 30px; /* Reduced padding */
    margin-bottom: 30px;
}

.form-container {
    display: flex;
    gap: 300px; /* Reduced gap */
    flex-wrap: wrap;
}

.left-column, .right-column {
    flex: 1;
    min-width: 150px; /* Reduced min width */
}

.form-group {
    display: flex;
    align-items: center;
    margin-bottom: 5px; /* Adjusted margin */
}

.form-group label, .form-group legend {
    flex: 0 0 80px; /* Adjusted width of labels */
    font-weight: bold;
    color: black;
    font-size: 15px; /* Adjusted font size */
}

input[readonly] {
    background: white;
    cursor: not-allowed;
}

button {
    background-color: black;
    color: #fff;
    border: none;
    padding: 4px 8px; /* Adjusted padding */
    border-radius: 3px;
    cursor: pointer;
    font-size: 9px; /* Adjusted font size */
    transition: background-color 0.3s;
}

button:hover {
    background-color: #0056b3;
}

.line {
    margin: 3px 0; /* Adjusted margin */
    border-top: 1px solid #e0e0e0;
}

.bottom2 {
    display: flex;
    flex-direction: column;
    gap: 5px; /* Adjusted gap */
}

input[type="submit"]:hover {
    background-color: #218838;
}

/* Adjusting specific column widths */
th:nth-child(5), td:nth-child(5) {
    width: 50px; /* Adjusted width for Total column */
}

</style>

    <script>
        function addTableRow(tableId, inputNames) {
            const table = document.getElementById(tableId).getElementsByTagName('tbody')[0];
            const newRow = table.insertRow();

            inputNames.forEach(name => {
                const cell = newRow.insertCell();
                cell.innerHTML = `<input type="text" name="${name}">`;
            });
        }

        function displayTimestamp() {
            const now = new Date();
            const timestamp = now.toISOString().slice(0, 19).replace('T', ' ');
            document.getElementById('issuedDate').value = timestamp;
        }

        function printPage() {
            window.print();
            
        }

        function addTableRow() {
        // Get the table by ID
        var table = document.getElementById(complainTable);

        // Create a new row element
        var newRow = table.insertRow();

        // Create the first cell (for the nature of complain input)
        var cell1 = newRow.insertCell(0);
        cell1.colSpan = 2;
        var input1 = document.createElement('input');
        input1.type = 'text';
        input1.name = inputNames[0];
        input1.style.width = '95%';
        input1.style.height = '80%';
        cell1.appendChild(input1);

        // Create the second cell (for the action taken input)
        var cell2 = newRow.insertCell(1);
        cell2.colSpan = 3;
        var input2 = document.createElement('input');
        input2.type = 'text';
        input2.name = inputNames[1];
        input2.style.width = '99%';
        input2.style.height = '80%';
        cell2.appendChild(input2);
    }
    function addTableRow() {
    // Get the table by ID
    var table = document.getElementById("replacement");
    
    // Create a new row
    var newRow = table.insertRow(-1); // Insert at the end of the table

    // Insert new cells in the new row
    for (var i = 0; i < 6; i++) {
        var newCell = newRow.insertCell(i);
        // Create an input element for each cell
        var inputElement;
        if (i < 5) { // For text inputs
            inputElement = document.createElement("input");
            inputElement.type = "text";
            inputElement.name = (i === 0 || i === 2) ? "model_no[]" : (i === 1) ? "new_sn[]" : (i === 3) ? "faulty_sn[]" : "location[]";
        } else { // For quantity input
            inputElement = document.createElement("input");
            inputElement.type = "number";
            inputElement.name = "qty[]";
        }

        // Style the input elements to match the original row
        inputElement.style.width = "100%"; // Makes the input take the full width of the cell
        inputElement.style.boxSizing = "border-box"; // Ensures padding and border are included in the element's total width and height
        
        // Optionally apply classes if your existing inputs have any specific classes
        // inputElement.className = "your-class-name"; // Uncomment and replace with actual class name if necessary
        
        newCell.appendChild(inputElement);
    }
}
    </script>
</head>
<body onload="displayTimestamp()">
    <div class="scaffold">

    <div class="logo">
        	<img src="images/TeleEye.png" border=0 height="70">
        </div>

        <form action="connect.php" method="POST">
        <div class="form-container">
                <div class="left-column">
                    <div class="form-group">
                        <label for="customerName">Customer Name:</label>
                        <input type="text" id="customerName" name="customerName" value="">
                    </div>

                    <div class="form-group">
                        <label for="address">Address:</label>
                        <input type="text" id="address" name="address" value="">
                    </div>

                    <div class="form-group">
                        <label for="location">Location:</label>
                        <input type="text" id="location" name="location" value="">
                    </div>

                    <div class="form-group">
                        <label for="requestedBy">Requested By:</label>
                        <input type="text" id="requestedBy" name="requestedBy" >
                    </div>

                    <div class="form-group">
                        <label for="branch">Branch:</label>
                        <input type="text" id="branch" name="branch">
                    </div>
                </div>

                <div class="right-column">
                    <div class="form-group">
                        <label for="casenumber">Case Number:</label>
                        <input type="text" id="casenumber" name="casenumber" >
                    </div>

                    <div class="form-group">
                        <label for="dateReported">Date Reported:</label>
                        <input type="date" id="dateReported" name="dateReported">
                    </div>

                    <div class="form-group">
                        <label for="issuedBy">Issued By:</label>
                        <input type="text" id="issuedBy" name="issuedBy" >
                    </div>

                    <div class="form-group">
                        <label for="assignedTechnician">Assigned Technician:</label>
                        <input type="text" id="assignedTechnician" name="assignedTechnician"  >
                    </div>

                    <div class="form-group">
                        <label for="contactNumber">Contact No.:</label>
                        <input type="tel" id="contactNumber" name="contactNumber"  >
                    </div>
                </div>
            </div>

            <div class="line"></div>

            <div class="middle">
            <section class="complain_body">
    <table border="1" cellpadding="3" class="report-table" id="complainTable" style="margin-bottom:15px; width:100%">
        <tr>
            <th colspan="2" align="left">NATURE OF COMPLAIN</th>
            <th colspan="3" align="left">ACTION TAKEN</th>
        </tr>
        <tr>
            <td colspan="2" align="left" valign="top" style="min-height:25px; height:25px;">
                <input type="text" name="nature_of_complain" style="width:95%; height:80%;">
            </td>
            <td colspan="3" align="left" valign="top">
                <input type="text" name="action_taken" style="width:99%; height:80%;">
            </td>
        </tr>
        <tr>
            <td colspan="2" align="left" valign="top" style="min-height:25px; height:25px;">
                <input type="text" name="nature_of_complain_2" style="width:95%; height:80%;">
            </td>
            <td colspan="3" align="left" valign="top">
                <input type="text" name="action_taken_2" style="width:99%; height:80%;">
            </td>
        </tr>
        <tr>
            <td colspan="2" align="left" valign="top" style="min-height:25px; height:25px;">
                <input type="text" name="nature_of_complain_3" style="width:95%; height:80%;">
            </td>
            <td colspan="3" align="left" valign="top">
                <input type="text" name="action_taken_3" style="width:99%; height:80%;">
            </td>
        </tr>
    </table>

    <!-- Add Row Button -->
    <button id="addComplain" onclick="addTableRow('complainTable', ['nature_of_complain', 'action_taken']); return false;">Add Row</button>

</section>
</div>


            <div class="line"></div>

            <table border="1" cellpadding="3" class="report-table" id="replacement" style="margin-bottom:15px; width:100%">
            <header width="220" align="left">PARTS/PRODUCTS REPLACED</header>
    <tr> 
        <th width="160" align="left">MODEL No.</th>
        <th width="130" align="center">New S/N</th>
        <th align="center">Model No.</th>
        <th width="110" align="center">Faulty S/N</th>
        <th width="110" align="center">Location</th>
        <th width="110" align="center">Qty</th>
    </tr>
    <tr>
        <td align="left"><input type="text" name="model_no[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="new_sn[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="model_no[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="faulty_sn[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="location[]" style="width: 85%;"></td>
        <td align="left"><input type="number" name="qty[]" style="width: 85%;" ></td>
    </tr>
    <tr>
        <td align="left"><input type="text" name="model_no[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="new_sn[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="model_no[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="faulty_sn[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="location[]" style="width: 85%;"></td>
        <td align="left"><input type="number" name="qty[]" style="width: 85%;" ></td>
    </tr>
    <tr>
        <td align="left"><input type="text" name="model_no[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="new_sn[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="model_no[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="faulty_sn[]" style="width: 85%;"></td>
        <td align="left"><input type="text" name="location[]" style="width: 85%;"></td>
        <td align="left"><input type="number" name="qty[]" style="width: 85%;" ></td>
    </tr>
</table>
<button id="addPart" onclick="addTableRow('report-table', ['model_no', 'new_sn','model_no','faulty_sn','location','qty']); return false;">Add Row</button>

            <div class="line"></div>

            <section class="item_body">
            <table border="1" cellpadding="2" class="report-table" style="margin-bottom:15px; width:100%; font-size: 10px;">
    <tr>
        <th colspan="4" align="center" style="font-size: 14px;">PERMISSION TO BRING BACK &amp; REMOVE PRODUCTS</th>
        <td width="150" rowspan="5" align="left" valign="top">
            <span class="small-font" style="font-size: 10px;">
                <b>CUSTOMER'S ACKNOWLEDGEMENT</b><br />
                ACCEPTED &amp; APPROVED BY:<br />
                (NAME)<br />
                CHOP &amp; SIGN<br />
            </span>
        </td>
    </tr>
    <tr>
        <td width="100" align="center"><b>ITEM(S)</b></td>
        <td width="40" align="center"><b>QTY</b></td>
        <td width="150" align="center"><b>TAKEN CONDITION</b></td>
        <td align="center"><b>REASON TO TAKE BACK</b></td>
    </tr>
    <tr>
        <td align="left"><input type="text" name="item_1"  style="width: 90%;" /></td>
        <td align="left"><input type="number" name="qty_1"  style="width: 90%;" /></td>
        <td align="left"><input type="text" name="condition_1"  style="width: 90%;" /></td>
        <td align="left"><input type="text" name="reason_1"  style="width: 90%;" /></td>
    </tr>
    <tr>
        <td align="left"><input type="text" name="item_2"  style="width: 90%;" /></td>
        <td align="left"><input type="number" name="qty_2"  style="width: 90%;" /></td>
        <td align="left"><input type="text" name="condition_2"  style="width: 90%;" /></td>
        <td align="left"><input type="text" name="reason_2"  style="width: 90%;" /></td>
    </tr>
    <tr>
        <td align="left"><input type="text" name="item_3"  style="width: 90%;" /></td>
        <td align="left"><input type="number" name="qty_3"  style="width: 90%;" /></td>
        <td align="left"><input type="text" name="condition_3"  style="width: 90%;" /></td>
        <td align="left"><input type="text" name="reason_3"  style="width: 90%;" /></td>
    </tr>
    <tr>
        <td colspan="5" align="center">
            <span class="small-font" style="font-size: 9px;">**THE ABOVE PRODUCT(S) IS/ARE BROUGHT BACK FOR FURTHER TROUBLESHOOTING, INSPECTION AND/OR REPAIR**</span>
        </td>
    </tr>
</table>


                
            </section>

            <div class="line"></div>

            <div class="bottom2">
            <table border="1" cellpadding="3" class="report-table" style="margin-bottom:15px; width:100%">
            <th width="50%" align="left" valign="top">
                        	<b>TELEEYE MALAYSIA SDN.BHD.</b><br />
                            <span class="small-font">
                        		SERVICE BY: <br/>
                                (NAME)<br/>
                                SIGNATURE:<br/><br/><br /><br />
                                TIME IN: ______________________________ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                                TIME OUT: ______________________________
						  </span> 
                        </th>
                        <td width="50%" align="left" valign="top">
                            <b>CUSTOMER'S ACKNOWLEDGEMENT</b><br />
                            <span class="small-font">
                            ACCEPTED &amp; APPROVED BY:<br />
                            (NAME)<br />
                            CHOP &amp; SIGN<br /></span></td>
            
                            </table>
                            <table border="1" cellpadding="3" class="report-table" style="margin-bottom:30px; width:100%">
                	<tr>
                      <th align="left">INTERNAL USE ONLY (TELEEYE MALAYSIA SDN.BHD.)</th>
                    </tr>
                	<tr>
                	  <td>REMARKS:</td>
              	  </tr>
                	<tr>
                	  <td>QUOTATION NO:</td>
              	  </tr>
                	<tr>
                	  <td>INVOICE NO:</td>
              	  </tr>
			</table>
    </div>
   <!-- Add this at the end of the form, right before the closing form tag -->
   <div style="text-align: center; margin-top: 10px;">
       <input type="submit" value="Submit Form">
   </div>
</body>

</html>
