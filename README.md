<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Author and Article Search Tool</title>
  <style>
    body {
      font-family: 'Helvetica Neue', Arial, sans-serif;
      margin: 20px;
      background-color: #f0f2f5;
      color: #333;
    }
    .search-container {
      display: flex;
      justify-content: center;
      align-items: center;
      margin-bottom: 20px;
      padding: 20px;
      background-color: #ffffff;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
      border-radius: 8px;
    }
    .results {
      margin-top: 20px;
    }
    .result-item {
      border: 1px solid #ddd;
      padding: 20px;
      margin-bottom: 15px;
      border-radius: 8px;
      background-color: #ffffff;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }
    .button-container {
      display: flex;
      gap: 10px;
      margin-top: 10px;
    }
    .button-container a {
      display: inline-block;
      width: 60px;
      height: 30px;
      border-radius: 8px;
      background-color: #fff;
      border: 2px solid #ccc;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
      transition: all 0.2s ease;
      overflow: hidden;
    }
    .button-container a img {
      width: 100%;
      height: auto;
    }
    .button-container a:hover {
      transform: translateY(-3px);
      background-color: #f9f9f9;
    }
    .loading {
      display: none;
      margin-top: 10px;
      font-size: 18px;
      color: #888;
      text-align: center;
    }
    h1 {
      text-align: center;
    }
    input[type="text"] {
      width: 80%;
      padding: 12px;
      font-size: 18px;
      border-radius: 5px;
      border: 1px solid #ccc;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    }
    button.search-btn {
      background-color: #4CAF50;
      color: white;
      border: none;
      padding: 12px 20px;
      font-size: 18px;
      border-radius: 5px;
      margin-left: 10px;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
      transition: transform 0.2s ease;
    }
    button.search-btn:hover {
      transform: translateY(-3px);
    }
  </style>
</head>
<body>
  <h1>Articles Search PPH</h1>
  <div class="search-container">
    <input type="text" id="searchQuery" placeholder="Enter Article Details">
    <button class="search-btn" onclick="searchAuthor()">Search</button>
  </div>
  <p class="loading" id="loading">Loading...</p>
  <div class="results" id="results"></div>

  <script>
    function searchAuthor() {
      const query = document.getElementById('searchQuery').value;
      if (!query) {
        alert('Please enter a search query');
        return;
      }
      document.getElementById('loading').style.display = 'block';
      const crossRefUrl = `https://api.crossref.org/works?query=${encodeURIComponent(query)}&rows=5`;
      fetch(crossRefUrl)
        .then(response => response.json())
        .then(data => {
          document.getElementById('loading').style.display = 'none';
          const resultsContainer = document.getElementById('results');
          resultsContainer.innerHTML = '';
          let works = data.message.items || [];
          if (works.length > 0) {
            works.forEach(work => {
              const title = work.title ? work.title[0] : 'Untitled';
              const authors = work.author || [];
              const doiLink = work.DOI || null;
              const authorLinks = authors.map(author => 
                `<a href="https://scholar.google.com/scholar?q=${encodeURIComponent(author.given + ' ' + author.family)}" target="_blank">${author.given} ${author.family}</a>`
              ).join(', ');
              let doiButton = doiLink ? `<a href="https://doi.org/${doiLink}" target="_blank"><img src="https://github.com/prakashsharma19/Referee/blob/main/Doi-removebg-preview.png?raw=true" alt="DOI"></a>` : '';
              let scholarButton = `<a href="https://scholar.google.com/scholar?q=${encodeURIComponent(title)}" target="_blank"><img src="https://github.com/prakashsharma19/Referee/blob/main/Google_scholar-removebg-preview.png?raw=true" alt="Google Scholar"></a>`;
              let arxivButton = `<a href="https://arxiv.org/search/?query=${encodeURIComponent(title)}&searchtype=all" target="_blank"><img src="https://github.com/prakashsharma19/Referee/blob/main/ArXiv%20image.png?raw=true" alt="ArXiv"></a>`;
              const resultItem = `
                <div class="result-item">
                  <h3><a href="https://www.google.com/search?q=${encodeURIComponent(title)}" target="_blank">${title}</a></h3>
                  <p class="author-line">by: ${authorLinks}</p>
                  <div class="button-container">
                    ${doiButton}
                    ${scholarButton}
                    ${arxivButton}
                  </div>
                </div>
              `;
              resultsContainer.innerHTML += resultItem;
            });
          } else {
            resultsContainer.innerHTML = `<p>No results found.</p>`;
          }
        })
        .catch(error => {
          console.error('Error fetching data:', error);
          alert('An error occurred while fetching data.');
          document.getElementById('loading').style.display = 'none';
        });
    }
  </script>
</body>
</html>
