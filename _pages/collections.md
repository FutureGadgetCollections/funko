---
title: "All Collections"
layout: single
permalink: /collection/
author_profile: false
classes: wide
---

Welcome to the Funko Pop collection! Browse the complete inventory organized by rarity.

## Collection Resources

- **[View Full Inventory on Google Sheets](https://docs.google.com/spreadsheets/d/e/2PACX-1vS3gfM7cviRIi6B-EN5sutoPyJEIDy_Kfsks7y-dGRE1ydop80zYjvEfAsF7qLfUtCTa9z7uzEO2c2O/pubhtml)**
- **[Browse on HobbyDB](https://www.hobbydb.com/marketplaces/hobbydb/users/futuregadgetlabs?show_list_stats=false&show_list_top_ten=false)**

---

<div id="inventoryContainer">
  <p id="loadingMessage">Loading inventory...</p>
  <div id="inventorySections"></div>
</div>

<style>
  .rarity-section {
    margin: 30px 0;
  }

  .rarity-section h2 {
    border-bottom: 2px solid #f2f2f2;
    padding-bottom: 10px;
    margin-bottom: 20px;
  }

  .rarity-stats {
    background-color: #f9f9f9;
    padding: 15px;
    border-radius: 5px;
    margin-bottom: 20px;
  }

  .rarity-stats p {
    margin: 5px 0;
  }

  .inventory-table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 20px;
  }

  .inventory-table th,
  .inventory-table td {
    border: 1px solid #ddd;
    padding: 10px;
    text-align: left;
  }

  .inventory-table th {
    background-color: #f2f2f2;
    font-weight: bold;
    cursor: pointer;
    user-select: none;
  }

  .inventory-table th:hover {
    background-color: #e0e0e0;
  }

  .inventory-table tr:nth-child(even) {
    background-color: #f9f9f9;
  }

  .inventory-table tr:hover {
    background-color: #f5f5f5;
  }

  .inventory-table a {
    color: #0066cc;
    text-decoration: none;
  }

  .inventory-table a:hover {
    text-decoration: underline;
  }
</style>

<script>
  (function() {
    const csvUrl = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vS3gfM7cviRIi6B-EN5sutoPyJEIDy_Kfsks7y-dGRE1ydop80zYjvEfAsF7qLfUtCTa9z7uzEO2c2O/pub?output=csv';

    // Rarity order for display
    const rarityOrder = [
      'LE Low Quantity Print Run',
      'LE Convention',
      'LE 7500',
      'LE 9500',
      'LE',
      'Common Exclusive Gamestop',
      'Common'
    ];

    const rarityDisplayNames = {
      'LE Low Quantity Print Run': 'Limited Edition - Low Quantity Print Run (<1,000 Units)',
      'LE Convention': 'Limited Edition - Convention Exclusive',
      'LE 7500': 'Limited Edition - 7,500 Units',
      'LE 9500': 'Limited Edition - 9,500 Units',
      'LE': 'Limited Edition',
      'Common Exclusive Gamestop': 'Common - GameStop Exclusive',
      'Common': 'Common'
    };

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
              i++;
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
        row.push(cell.trim());
        result.push(row);
      }

      return result;
    }

    function formatCurrency(value) {
      const num = parseFloat(value);
      return isNaN(num) ? value : '$' + num.toFixed(2);
    }

    function createRaritySection(rarity, items, headers) {
      const displayName = rarityDisplayNames[rarity] || rarity;
      const section = document.createElement('div');
      section.className = 'rarity-section';
      const sectionId = rarity.replace(/\s+/g, '-').toLowerCase();

      // Calculate stats
      const totalUnits = items.reduce((sum, item) => sum + parseInt(item.quantity || 0), 0);
      const totalValue = items.reduce((sum, item) => sum + (parseFloat(item.marketValue || 0) * parseInt(item.quantity || 0)), 0);
      const uniqueItems = items.length;

      let html = `
        <h2>${displayName}</h2>
        <div class="rarity-stats">
          <p><strong>Total Units:</strong> ${totalUnits}</p>
          <p><strong>Unique Items:</strong> ${uniqueItems}</p>
          <p><strong>Total Market Value:</strong> ${formatCurrency(totalValue)}</p>
        </div>
        <input type="text"
               id="search-${sectionId}"
               class="section-search"
               placeholder="Filter by franchise or item name..."
               style="width: 100%; padding: 8px; margin-bottom: 10px; box-sizing: border-box;">
        <table class="inventory-table" id="table-${sectionId}">
          <thead>
            <tr>
              <th>Franchise</th>
              <th>Item</th>
              <th>SKU</th>
              <th>Quantity</th>
              <th>Market Value</th>
              <th>Purchase Price</th>
              <th>Purchase Date</th>
            </tr>
          </thead>
          <tbody>
      `;

      items.forEach(item => {
        const link = item.link && item.link !== '' ? item.link : '#';
        const itemName = link !== '#' ? `<a href="${link}" target="_blank">${item.item}</a>` : item.item;

        html += `
          <tr data-franchise="${item.franchise.toLowerCase()}" data-item="${item.item.toLowerCase()}">
            <td>${item.franchise}</td>
            <td>${itemName}</td>
            <td>${item.sku}</td>
            <td>${item.quantity}</td>
            <td>${formatCurrency(item.marketValue)}</td>
            <td>${formatCurrency(item.purchasePrice)}</td>
            <td>${item.purchaseDate}</td>
          </tr>
        `;
      });

      html += `
          </tbody>
        </table>
      `;

      section.innerHTML = html;

      // Add search functionality
      setTimeout(() => {
        const searchInput = document.getElementById(`search-${sectionId}`);
        const table = document.getElementById(`table-${sectionId}`);

        if (searchInput && table) {
          searchInput.addEventListener('input', function() {
            const filter = this.value.toLowerCase();
            const rows = table.querySelectorAll('tbody tr');

            rows.forEach(row => {
              const franchise = row.getAttribute('data-franchise');
              const item = row.getAttribute('data-item');

              if (franchise.includes(filter) || item.includes(filter)) {
                row.style.display = '';
              } else {
                row.style.display = 'none';
              }
            });
          });
        }
      }, 100);

      return section;
    }

    fetch(csvUrl)
      .then(response => response.text())
      .then(csv => {
        const data = parseCSV(csv);
        if (data.length === 0) return;

        const headers = data[0];
        const rows = data.slice(1);

        // Find column indices
        const colIndex = {
          rarity: headers.findIndex(h => h.toLowerCase().includes('rarity')),
          franchise: headers.findIndex(h => h.toLowerCase().includes('franchise')),
          item: headers.findIndex(h => h.toLowerCase().includes('item')),
          sku: headers.findIndex(h => h.toLowerCase().includes('sku')),
          quantity: headers.findIndex(h => h.toLowerCase().includes('quantity')),
          marketValue: headers.findIndex(h => h.toLowerCase().includes('market value')),
          link: headers.findIndex(h => h.toLowerCase().includes('link')),
          purchaseDate: headers.findIndex(h => h.toLowerCase().includes('purchase date')),
          purchasePrice: headers.findIndex(h => h.toLowerCase().includes('purchase price'))
        };

        // Group items by rarity
        const itemsByRarity = {};
        rows.forEach(row => {
          if (row.length < headers.length) return;

          const rarity = row[colIndex.rarity];
          if (!rarity) return;

          if (!itemsByRarity[rarity]) {
            itemsByRarity[rarity] = [];
          }

          itemsByRarity[rarity].push({
            rarity: rarity,
            franchise: row[colIndex.franchise],
            item: row[colIndex.item],
            sku: row[colIndex.sku],
            quantity: row[colIndex.quantity],
            marketValue: row[colIndex.marketValue],
            link: row[colIndex.link],
            purchaseDate: row[colIndex.purchaseDate],
            purchasePrice: row[colIndex.purchasePrice]
          });
        });

        // Create sections in order
        const container = document.getElementById('inventorySections');
        rarityOrder.forEach(rarity => {
          if (itemsByRarity[rarity]) {
            const section = createRaritySection(rarity, itemsByRarity[rarity], headers);
            container.appendChild(section);
          }
        });

        // Add any remaining rarities not in the predefined order
        Object.keys(itemsByRarity).forEach(rarity => {
          if (!rarityOrder.includes(rarity)) {
            const section = createRaritySection(rarity, itemsByRarity[rarity], headers);
            container.appendChild(section);
          }
        });

        document.getElementById('loadingMessage').style.display = 'none';
      })
      .catch(error => {
        document.getElementById('loadingMessage').textContent = 'Error loading inventory: ' + error.message;
      });
  })();
</script>
