---
permalink: /portfolio-summary/
title: "Portfolio Summary"
layout: archive
author_profile: false
classes: wide
---

This section showcases portfolio summary entries and analysis.

## Transaction Summary

View the complete transaction summary in [Google Sheets](https://docs.google.com/spreadsheets/d/e/2PACX-1vQg-yzv0kwaQS58YbN-y-ofIRF4QbgiYkuoF_w02uuEsDA8Z4wqc923_p8jEu2b_LsrHEoBfDr3zL4v/pubhtml?gid=0&single=true).

<div id="transactionTableContainer">
  <input type="text" id="transactionSearchInput" placeholder="Filter table..." style="width: 100%; padding: 8px; margin-bottom: 10px; box-sizing: border-box;">
  <div style="overflow-x: auto;">
    <table id="transactionTable" class="data-table">
      <thead id="transactionTableHead"></thead>
      <tbody id="transactionTableBody"></tbody>
    </table>
  </div>
  <p id="transactionLoadingMessage">Loading transaction data...</p>
</div>

## IRR (Internal Rate of Return)

View the complete IRR analysis in [Google Sheets](https://docs.google.com/spreadsheets/d/e/2PACX-1vQg-yzv0kwaQS58YbN-y-ofIRF4QbgiYkuoF_w02uuEsDA8Z4wqc923_p8jEu2b_LsrHEoBfDr3zL4v/pubhtml?gid=1132874555&single=true).

<div id="irrTableContainer">
  <input type="text" id="irrSearchInput" placeholder="Filter table..." style="width: 100%; padding: 8px; margin-bottom: 10px; box-sizing: border-box;">
  <div style="overflow-x: auto;">
    <table id="irrTable" class="data-table">
      <thead id="irrTableHead"></thead>
      <tbody id="irrTableBody"></tbody>
    </table>
  </div>
  <p id="irrLoadingMessage">Loading IRR data...</p>
</div>

## Inventory Summary

### HobbyDB Collection

Browse my complete collection on [HobbyDB](https://www.hobbydb.com/marketplaces/hobbydb/users/futuregadgetlabs?show_list_stats=false&show_list_top_ten=false).

### Inventory Details

View the complete inventory in [Google Sheets](https://docs.google.com/spreadsheets/d/e/2PACX-1vS3gfM7cviRIi6B-EN5sutoPyJEIDy_Kfsks7y-dGRE1ydop80zYjvEfAsF7qLfUtCTa9z7uzEO2c2O/pubhtml).

<div id="inventoryTableContainer">
  <input type="text" id="inventorySearchInput" placeholder="Filter table..." style="width: 100%; padding: 8px; margin-bottom: 10px; box-sizing: border-box;">
  <div style="overflow-x: auto;">
    <table id="inventoryTable" class="data-table">
      <thead id="inventoryTableHead"></thead>
      <tbody id="inventoryTableBody"></tbody>
    </table>
  </div>
  <p id="inventoryLoadingMessage">Loading inventory data...</p>
</div>

<style>
  .data-table {
    width: 100%;
    border-collapse: collapse;
    border: 1px solid #ddd;
  }
  .data-table th, .data-table td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: left;
  }
  .data-table th {
    background-color: #f2f2f2;
    cursor: pointer;
    user-select: none;
  }
  .data-table th:hover {
    background-color: #e0e0e0;
  }
  .data-table tr:nth-child(even) {
    background-color: #f9f9f9;
  }
  .data-table tr:hover {
    background-color: #f5f5f5;
  }
</style>

<script>
  (function() {
    // Configuration for each table
    const tables = [
      {
        name: 'transaction',
        csvUrl: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vQg-yzv0kwaQS58YbN-y-ofIRF4QbgiYkuoF_w02uuEsDA8Z4wqc923_p8jEu2b_LsrHEoBfDr3zL4v/pub?gid=0&single=true&output=csv',
        tableHeadId: 'transactionTableHead',
        tableBodyId: 'transactionTableBody',
        searchInputId: 'transactionSearchInput',
        loadingMessageId: 'transactionLoadingMessage'
      },
      {
        name: 'irr',
        csvUrl: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vQg-yzv0kwaQS58YbN-y-ofIRF4QbgiYkuoF_w02uuEsDA8Z4wqc923_p8jEu2b_LsrHEoBfDr3zL4v/pub?gid=1132874555&single=true&output=csv',
        tableHeadId: 'irrTableHead',
        tableBodyId: 'irrTableBody',
        searchInputId: 'irrSearchInput',
        loadingMessageId: 'irrLoadingMessage'
      },
      {
        name: 'inventory',
        csvUrl: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vS3gfM7cviRIi6B-EN5sutoPyJEIDy_Kfsks7y-dGRE1ydop80zYjvEfAsF7qLfUtCTa9z7uzEO2c2O/pub?output=csv',
        tableHeadId: 'inventoryTableHead',
        tableBodyId: 'inventoryTableBody',
        searchInputId: 'inventorySearchInput',
        loadingMessageId: 'inventoryLoadingMessage'
      }
    ];

    function parseCSV(csv) {
      const lines = csv.split('\n');
      const result = [];

      for (let line of lines) {
        if (!line.trim()) continue;

        const row = [];
        let cell = '';
        let inQuotes = false;

        for (let i = 0; i < line.length; i++) {
          const char = line[i];
          const nextChar = line[i + 1];

          if (char === '"') {
            if (inQuotes && nextChar === '"') {
              cell += '"';
              i++; // Skip next quote
            } else {
              inQuotes = !inQuotes;
            }
          } else if (char === ',' && !inQuotes) {
            row.push(cell.trim());
            cell = '';
          } else {
            cell += char;
          }
        }
        row.push(cell.trim()); // Add last cell
        result.push(row);
      }

      return result;
    }

    function createTableManager(config) {
      let tableData = [];
      let sortColumn = -1;
      let sortAscending = true;

      function renderTable(data, filter = '') {
        const tableHead = document.getElementById(config.tableHeadId);
        const tableBody = document.getElementById(config.tableBodyId);

        if (data.length === 0) return;

        // Create header
        const headers = data[0];
        let headerHTML = '<tr>';
        headers.forEach((header, index) => {
          const arrow = sortColumn === index ? (sortAscending ? ' ▲' : ' ▼') : '';
          headerHTML += `<th onclick="window.sortTable_${config.name}(${index})">${header}${arrow}</th>`;
        });
        headerHTML += '</tr>';
        tableHead.innerHTML = headerHTML;

        // Filter and create body
        const filterLower = filter.toLowerCase();
        let bodyHTML = '';
        for (let i = 1; i < data.length; i++) {
          const row = data[i];
          if (filter === '' || row.some(cell => cell.toLowerCase().includes(filterLower))) {
            bodyHTML += '<tr>';
            row.forEach(cell => {
              bodyHTML += `<td>${cell}</td>`;
            });
            bodyHTML += '</tr>';
          }
        }
        tableBody.innerHTML = bodyHTML;
      }

      window[`sortTable_${config.name}`] = function(columnIndex) {
        if (sortColumn === columnIndex) {
          sortAscending = !sortAscending;
        } else {
          sortColumn = columnIndex;
          sortAscending = true;
        }

        const headers = tableData[0];
        const rows = tableData.slice(1);

        rows.sort((a, b) => {
          let aVal = a[columnIndex];
          let bVal = b[columnIndex];

          // Try to parse as numbers
          const aNum = parseFloat(aVal);
          const bNum = parseFloat(bVal);

          if (!isNaN(aNum) && !isNaN(bNum)) {
            return sortAscending ? aNum - bNum : bNum - aNum;
          }

          // String comparison
          return sortAscending
            ? aVal.localeCompare(bVal)
            : bVal.localeCompare(aVal);
        });

        tableData = [headers, ...rows];
        const searchInput = document.getElementById(config.searchInputId);
        renderTable(tableData, searchInput.value);
      };

      // Fetch and initialize table
      fetch(config.csvUrl)
        .then(response => response.text())
        .then(csv => {
          tableData = parseCSV(csv);
          renderTable(tableData);
          document.getElementById(config.loadingMessageId).style.display = 'none';

          // Add search functionality
          const searchInput = document.getElementById(config.searchInputId);
          searchInput.addEventListener('input', function() {
            renderTable(tableData, this.value);
          });
        })
        .catch(error => {
          document.getElementById(config.loadingMessageId).textContent = 'Error loading data: ' + error.message;
        });
    }

    // Initialize all tables
    tables.forEach(config => createTableManager(config));
  })();
</script>

## Portfolio Entries

{% assign portfolio_posts = site.categories.portfolio-summary %}
{% if portfolio_posts.size > 0 %}
  {% for post in portfolio_posts reversed %}
    {% include archive-single.html %}
  {% endfor %}
{% else %}
  *No portfolio summary entries yet.*
{% endif %}
