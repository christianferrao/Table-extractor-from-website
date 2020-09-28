// ==UserScript==
// @name         BQE_download_button_version2
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  try to take over the world!
// @author       ferraoc
// @match        https://browse-query-editor-eu.aka.amazon.com/*
// @grant        none
// @require      http://code.jquery.com/jquery-3.5.0.min.js
// ==/UserScript==

(function() {
var dataDownloadBtn;
//    var downloadTable;
    document.addEventListener('mouseover', function createButton() {
      //  var resultsTable;

        dataDownloadBtn = document.createElement("button");
        dataDownloadBtn.setAttribute("class", "btn btn-primary");
        dataDownloadBtn.innerText = "Get me those data!";
      //  dataDownloadBtn.setAttribute("class", "ui-button ui-widget ui-state-default ui-corner-all ui-button-text-icon-right ng-star-inserted");
        dataDownloadBtn.setAttribute("id", "coolButton");
 //       dataDownloadBtn.style.left = "100px";
        dataDownloadBtn.style.backgroundColor = "orange";
        dataDownloadBtn.addEventListener("click", downlodadBQETable);
        var div1 = document.createElement('div')
        document.getElementsByClassName('row')[7].appendChild(div1)
        div1.setAttribute('class', 'col-xs-4 col-xs-offset-4')
        div1.appendChild(dataDownloadBtn);
 //       document.querySelector("#ngb-tab-0-panel > az-schedule-management > az-schedule-quick-search > div > form").appendChild(dataDownloadBtn);
    }, {once : true } );

    function downlodadBQETable() {
        var ths = document.getElementsByTagName('table')[0].getElementsByTagName('th')
        var tds = document.getElementsByTagName('table')[0].getElementsByTagName('td')
        var trs = document.getElementsByTagName('table')[0].getElementsByTagName('tr')

        var listofHeads = [];
        var tableArray = [];

        for (let index = 0; index < ths.length; index++) {
            const head = ths[index].innerText;
            const headLength = head.length;
            if (headLength != 0 ) {
                if ( head != 'image') {
                    listofHeads.push(head)
                };
            };
        };

        tableArray.push([listofHeads.join('\t')])

        var rowArray = [];


        for (let indexR = 1; indexR < trs.length; indexR++) {
            const elementRow = trs[indexR].innerText.replace(/\n/g, " ");
            const row = trs[indexR];
            const elementRowClass = trs[indexR].lastChild.className;
            if (elementRowClass.includes('danger', 0) == true) {
                rowArray.push(elementRow.trim().split(' ')[0]);
                var arrayInvalid = 'Invalid_Asin '.repeat(listofHeads.length - 1).split(' ')
                arrayInvalid.pop();
                for (let word of arrayInvalid) { rowArray.push(word); }
              //  tableArray.push([rowArray.join('\t')])
          //      rowArray = [];
            } else {
                for (let cell of row.cells)
                {
                    const cellClass = cell.className;
                    const cellText = cell.innerText;
                    if (['image', 'checkbox'].includes(cellClass.split('--').pop())==false) {rowArray.push(cellText.replace(/\n/g, " "))}
                    else {if (cellClass.split('--').pop() == 'empty') {
                        rowArray.push(' ')
                    }
                         }
                }
            }
            tableArray.push([rowArray.join('\t')]);
            rowArray = [];
        };
        var tableArray1 = tableArray.join('\n').replace(/ Where Is My ASIN CSI Corrections|{ value:|}|{ language_tag:de_DE, value:/g, '')
        var blob = new Blob([tableArray1], {
            // type: 'text/csv'
            //    type: 'application/vnd.ms-excel'
            //type: "text/plain;charset=utf-8;"
            type: 'data:application/vnd.ms-excel;charset=UTF-8;base64,'
        });
        var elem = window.document.createElement('a');
        elem.href = window.URL.createObjectURL(blob);
        elem.download = "BQE_table_data.xls";
        document.body.appendChild(elem);
        elem.click();
        document.body.removeChild(elem);
}
})();
