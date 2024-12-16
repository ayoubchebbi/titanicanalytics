import { LightningElement, track, wire } from 'lwc';
import { loadScript } from 'lightning/platformResourceLoader';
import SHEETJS from '@salesforce/resourceUrl/sheetjs'; // Replace 'sheetjs' with your static resource name

export default class MyComponent extends LightningElement {
    @track sheetJSLoaded = false;

    // Load SheetJS library
    connectedCallback() {
        loadScript(this, SHEETJS)
            .then(() => {
                // SheetJS library loaded successfully
                this.sheetJSLoaded = true;
            })
            .catch(error => {
                // Handle error loading the script
                console.error('Error loading SheetJS library: ', error);
            });
    }

    // Function to generate Excel
    generateExcel() {
        if (!this.sheetJSLoaded) {
            console.error('SheetJS library is not loaded yet.');
            return;
        }

        // Your Excel generation code using SheetJS
        // Example:
        const workbook = XLSX.utils.book_new();
        const worksheet = XLSX.utils.aoa_to_sheet([['Hello', 'World']]);
        XLSX.utils.book_append_sheet(workbook, worksheet, 'Sheet1');
        XLSX.writeFile(workbook, 'example.xlsx');
    }
}
public static void deleteDuplicateRecord(List<SObject> newObjects) {
    // Set to store unique keys for new records
    Set<String> newRecordKeys = new Set<String>();

    // Map to store existing records with their keys for quick lookup
    Map<String, WOMCO_PatientStockControlItem__c> existingRecordMap = new Map<String, WOMCO_PatientStockControlItem__c>();

    // List to collect records that need to be deleted
    List<WOMCO_PatientStockControlItem__c> recordsToDelete = new List<WOMCO_PatientStockControlItem__c>();

    // Sets to store unique field values for querying existing records
    Set<String> uniqueSerialNumbers = new Set<String>();
    Set<String> uniqueProducts = new Set<String>();
    Set<String> uniqueControlStocks = new Set<String>();

    // Loop through the incoming records to generate unique keys
    for (WOMCO_PatientStockControlItem__c newItem : (List<WOMCO_PatientStockControlItem__c>) newObjects) {
        // Create a composite key using fields
        String key = newItem.WOMCO_Product__c + '|' + newItem.WOMCO_SerialNumber__c + '|' + newItem.WOMCO_PatientStockControl__c;
        
        // If the key is not already in the set, add it and collect unique field values
        if (!newRecordKeys.contains(key)) {
            newRecordKeys.add(key);
            uniqueSerialNumbers.add(newItem.WOMCO_SerialNumber__c);
            uniqueProducts.add(newItem.WOMCO_Product__c);
            uniqueControlStocks.add(newItem.WOMCO_PatientStockControl__c);
        }
    }

    // If there are new unique keys, proceed to query existing records
    if (!newRecordKeys.isEmpty()) {
        // Query existing records that match the collected unique field values
        List<WOMCO_PatientStockControlItem__c> existingRecords = [
            SELECT Id, WOMCO_Product__c, WOMCO_SerialNumber__c, WOMCO_PatientStockControl__c
            FROM WOMCO_PatientStockControlItem__c
            WHERE WOMCO_Product__c IN :uniqueProducts
              AND WOMCO_SerialNumber__c IN :uniqueSerialNumbers
              AND WOMCO_PatientStockControl__c IN :uniqueControlStocks
        ];

        // Populate the map with existing records for quick access using their composite keys
        for (WOMCO_PatientStockControlItem__c existingItem : existingRecords) {
            String key = existingItem.WOMCO_Product__c + '|' + existingItem.WOMCO_SerialNumber__c + '|' + existingItem.WOMCO_PatientStockControl__c;
            existingRecordMap.put(key, existingItem);
        }

        // Identify records in the database that have the same keys as the new ones
        for (WOMCO_PatientStockControlItem__c newItem : (List<WOMCO_PatientStockControlItem__c>) newObjects) {
            String key = newItem.WOMCO_Product__c + '|' + newItem.WOMCO_SerialNumber__c + '|' + newItem.WOMCO_PatientStockControl__c;

            // If the key exists in the map, add the corresponding database record to the deletion list
            if (existingRecordMap.containsKey(key)) {
                recordsToDelete.add(existingRecordMap.get(key));
            }
        }

        // Perform the deletion of identified records
        if (!recordsToDelete.isEmpty()) {
            Database.delete(recordsToDelete, false); // Use partial delete to continue on errors
        }
    }
}



# titanicanalytics
The sinking of the RMS Titanic is one of the most infamous shipwrecks in history.  On April 15, 1912, during her maiden voyage, the Titanic sank after colliding with an iceberg, killing 1502 out of 2224 passengers and crew. This sensational tragedy shocked the international community and led to better safety regulations for ships.  One of the reasons that the shipwreck led to such loss of life was that there were not enough lifeboats for the passengers and crew. Although there was some element of luck involved in surviving the sinking, some groups of people were more likely to survive than others, such as women, children, and the upper-class.  In this challenge, we ask you to complete the analysis of what sorts of people were likely to survive. In particular, we ask you to apply the tools of machine learning to predict which passengers survived the tragedy.
