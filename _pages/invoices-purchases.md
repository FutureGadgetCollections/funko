---
title: "Purchases"
layout: archive
permalink: /invoices/purchases/
author_profile: false
classes: wide
---

All purchase transactions for the LEGO collection.

## Purchase Summary

View the complete purchase summary in [Google Sheets](https://docs.google.com/spreadsheets/d/e/2PACX-1vQzHldLkJTUy2cHpy4cqtcSgIz9l2zaATGyKMmC1YD-ZCpoihX8SYiAQ6SJjd8ohgLaw5U2tNEDFi7y/pubhtml).

<div id="tableContainer">
  <input type="text" id="searchInput" placeholder="Filter table..." style="width: 100%; padding: 8px; margin-bottom: 10px; box-sizing: border-box;">
  <div style="overflow-x: auto;">
    <table id="purchaseTable" style="width: 100%; border-collapse: collapse;">
      <thead id="tableHead"></thead>
      <tbody id="tableBody"></tbody>
    </table>
  </div>
  <p id="loadingMessage">Loading purchase data...</p>
</div>

<style>
  #purchaseTable {
    border: 1px solid #ddd;
  }
  #purchaseTable th, #purchaseTable td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: left;
  }
  #purchaseTable th {
    background-color: #f2f2f2;
    cursor: pointer;
    user-select: none;
  }
  #purchaseTable th:hover {
    background-color: #e0e0e0;
  }
  #purchaseTable tr:nth-child(even) {
    background-color: #f9f9f9;
  }
  #purchaseTable tr:hover {
    background-color: #f5f5f5;
  }
</style>

<script>
  (function() {
    const csvUrl = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vQzHldLkJTUy2cHpy4cqtcSgIz9l2zaATGyKMmC1YD-ZCpoihX8SYiAQ6SJjd8ohgLaw5U2tNEDFi7y/pub?output=csv';
    let tableData = [];
    let sortColumn = -1;
    let sortAscending = true;

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

    function renderTable(data, filter = '') {
      const tableHead = document.getElementById('tableHead');
      const tableBody = document.getElementById('tableBody');

      if (data.length === 0) return;

      // Create header
      const headers = data[0];
      let headerHTML = '<tr>';
      headers.forEach((header, index) => {
        const arrow = sortColumn === index ? (sortAscending ? ' ▲' : ' ▼') : '';
        headerHTML += `<th onclick="sortTable(${index})">${header}${arrow}</th>`;
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

    window.sortTable = function(columnIndex) {
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
      const searchInput = document.getElementById('searchInput');
      renderTable(tableData, searchInput.value);
    };

    fetch(csvUrl)
      .then(response => response.text())
      .then(csv => {
        tableData = parseCSV(csv);
        renderTable(tableData);
        document.getElementById('loadingMessage').style.display = 'none';

        // Add search functionality
        const searchInput = document.getElementById('searchInput');
        searchInput.addEventListener('input', function() {
          renderTable(tableData, this.value);
        });
      })
      .catch(error => {
        document.getElementById('loadingMessage').textContent = 'Error loading data: ' + error.message;
      });
  })();
</script>

{% assign purchase_posts = site.categories.purchases %}
{% if purchase_posts.size > 0 %}
  {% assign posts_by_year = purchase_posts | group_by_exp: "post", "post.date | date: '%Y'" %}
  {% for year in posts_by_year %}

## {{ year.name }}

{% for post in year.items reversed %}
      {% include archive-single.html %}
    {% endfor %}
  {% endfor %}
{% else %}
  *No purchase records yet.*
{% endif %}
