# Bookshelf-Apps
HTML
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bookshelf Apps</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Bookshelf Apps</h1>
  <form id="add-book-form">
    <label for="title">Title:</label>
    <input type="text" id="title" required>
    <br>
    <label for="author">Author:</label>
    <input type="text" id="author" required>
    <br>
    <label for="year">Year:</label>
    <input type="number" id="year" required>
    <br>
    <label for="isComplete">Is Complete:</label>
    <input type="checkbox" id="isComplete">
    <br>
    <button type="submit">Add Book</button>
  </form>
  <div id="bookshelf">
    <h2>Belum selesai dibaca</h2>
    <ul id="unfinished-books"></ul>
    <h2>Selesai dibaca</h2>
    <ul id="finished-books"></ul>
  </div>
  <script src="script.js"></script>
</body>
</html>


CSS
body {
  font-family: Arial, sans-serif;
  text-align: center;
}

#bookshelf {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
}

#unfinished-books, #finished-books {
  list-style: none;
  padding: 0;
  margin: 0;
}

#unfinished-books li, #finished-books li {
  padding: 10px;
  border-bottom: 1px solid #ccc;
}

#unfinished-books li:hover, #finished-books li:hover {
  background-color: #f0f0f0;
  cursor: pointer;
}

button[type="submit"] {
  background-color: #4CAF50;
  color: #ffffff;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button[type="submit"]:hover {
  background-color: #3e8e41;
}


Javascript
const bookshelf = {
  unfinishedBooks: [],
  finishedBooks: [],
  addBook: function(book) {
    if (book.isComplete) {
      this.finishedBooks.push(book);
    } else {
      this.unfinishedBooks.push(book);
    }
    this.saveToLocalStorage();
    this.renderBooks();
  },
  removeBook: function(bookId, isComplete) {
    if (isComplete) {
      this.finishedBooks = this.finishedBooks.filter(book => book.id!== bookId);
    } else {
      this.unfinishedBooks = this.unfinishedBooks.filter(book => book.id!== bookId);
    }
    this.saveToLocalStorage();
    this.renderBooks();
  },
  moveBook: function(bookId, isComplete) {
    const book = this.getBookById(bookId);
    if (book) {
      if (isComplete) {
        this.finishedBooks.push(book);
        this.unfinishedBooks = this.unfinishedBooks.filter(book => book.id!== bookId);
      } else {
        this.unfinishedBooks.push(book);
        this.finishedBooks = this.finishedBooks.filter(book => book.id!== bookId);
      }
      this.saveToLocalStorage();
      this.renderBooks();
    }
  },
  getBookById: function(bookId) {
    return [...this.unfinishedBooks,...this.finishedBooks].find(book => book.id === bookId);
  },
  saveToLocalStorage: function() {
    localStorage.setItem('bookshelf', JSON.stringify(this));
  },
  loadFromLocalStorage: function() {
    const storedBookshelf = localStorage.getItem('bookshelf');
    if (storedBookshelf) {
      const parsedBookshelf = JSON.parse(storedBookshelf);
      this.unfinishedBooks = parsedBookshelf.unfinishedBooks;
      this.finishedBooks = parsedBookshelf.finishedBooks;
    }
  },
  renderBooks: function() {
    const unfinishedBooksHTML = this.unfinishedBooks.map(book => `
      <li>
        <span>${book.title} by ${book.author}</span>
        <button onclick="bookshelf.removeBook(${book.id}, false)">Remove</button>
        <button onclick="bookshelf.moveBook(${book.id}, true)">Move to Finished</button>
      </li>
    `).join('');
    const finishedBooksHTML = this.finishedBooks.map(book => `
      <li>
        <span>${book.title} by ${book.author}</span>
        <button onclick="bookshelf.removeBook(${book.id}, true)">Remove</button>
        <button onclick="bookshelf.moveBook(${book.id}, false)">Move to Unfinished</button>
      </li>
    `).join('');
    document.getElementById('unfinished-books').innerHTML = unfinishedBooksHTML;
    document.getElementById('finished-books').innerHTML = finishedBooksHTML;
  }
};

document.getElementById('add-book-form').addEventListener('submit', function(event) {
  event.preventDefault();
  const title = document.getElementById('title').value;
  const author = document.getElementById('author').value;
  const year = document.getElementById('year').value;
  const isComplete = document.getElementById('isComplete').checked;
  const book = {
    id: +new Date(),
    title,
    author,
    year: parseInt(year),
    isComplete
  };
  bookshelf.addBook(book);
});

bookshelf.loadFromLocalStorage();
bookshelf.renderBooks();
