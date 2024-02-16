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



# titanicanalytics
The sinking of the RMS Titanic is one of the most infamous shipwrecks in history.  On April 15, 1912, during her maiden voyage, the Titanic sank after colliding with an iceberg, killing 1502 out of 2224 passengers and crew. This sensational tragedy shocked the international community and led to better safety regulations for ships.  One of the reasons that the shipwreck led to such loss of life was that there were not enough lifeboats for the passengers and crew. Although there was some element of luck involved in surviving the sinking, some groups of people were more likely to survive than others, such as women, children, and the upper-class.  In this challenge, we ask you to complete the analysis of what sorts of people were likely to survive. In particular, we ask you to apply the tools of machine learning to predict which passengers survived the tragedy.
