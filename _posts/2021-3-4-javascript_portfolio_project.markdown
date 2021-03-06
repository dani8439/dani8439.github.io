# Project time

For my JavaScript portfolio project, I created a Single Page Application called Shelfie. The purpose of the application is for a user to be able to save 
books of interest along with normal properties of books (title, author, a brief summary), as well as any quotes the user found interesting, or wonderful, or 
for any of the reasons why a quote stands out. 

# The first hurdle

I wanted a user to be able to save both a book, and multiple quotes at the time of initial instantiation of a book object. I wanted the models to be that a 
Book model `has_many :quotes` and that Quote model. `belongs_to :book`. 

Reading the ActiveRecord documentation, by default, nested attribute updating is turned off. But it can be turned on by using the `accepts_nested_attributes_for :quotes` in the parent model of Book. This then defines an attribute writer on the parent model for the child in order to write all associated attributes. In the case of a one-to-many association, there were a few hiccups, that in retrospect seemed stupid but are very easy to make. 

Strong params of the parent model has to be set up properly for this association to work, and to do that, you must pluralize the child model in the permit method. Any associated child model becomes `params.require(:parent_model).permit(:attribute_of_parent_model, :childs_attributes => [:child_attributes])`. My `Book` model looked like below, but getting there and catching the additional s produced a lot of headaches, something that even in an open office hours was not caught, as it's honestly something so simple and easy to miss. 

```
  private

  def book_params
    params.require(:book).permit(:id, :title, :author, :summary, :quotes_attributes => [:id, :quote, :_destroy])
  end
```

You also have to remember to build on top of the parent model in the new method, so that my new in BooksController looked like this in order to save each new
instance of the quote class:

```
  def new
    book = Book.new; book.quotes.build
  end
```


Once the models were set up properly and working, passing the information through the front end to back end is pretty straightforward. So long as you remember to pluralize things correctly when the data is sent through the fetch call from the browser to the server. As you can tell, I did not remember to do that initially, and so while a book model was saving perfectly well, I was getting the dreaded `undefined` for all the children. Once the error was caught, remembering to pluralize the data as quotes_attributes, and then passing it as as array of hashes of all the children means that a user can save a single book instance, with five children.


```
  function postFetch(title, author, summary, quote1, quote2, quote3, quote4, quote5){
    console.log(title, author, summary, quote1, quote2, quote3, quote4, quote5)
     fetch(endPoint, {
      method: "POST",
      headers: {"Content-Type": "application/json"},
      body: JSON.stringify({
        title: title,
        author: author,
        summary: summary,
        quotes_attributes: [
          {
            quote: quote1,
          },
          {
            quote: quote2
          },
          {
            quote: quote3
          },
          {
            quote: quote4
          },
          {
            quote: quote5
          }
        ]
      })
    })
    .then(response => response.json())
    .then(book => {

      console.log(book.data);
      const bookData = book.data;
      let newBook = new Book(bookData, bookData.attributes)

      document.querySelector('#book-container').innerHTML += newBook.renderBookCard();

    })
  }
```

# Why stop at just five?

While initially a user can save up to five quotes for a book, in reality I wanted a user to have the capability of saving as many quotes as possible to any given book. In order to do that, playing around with looping, and hidden_fields made such a thing possible. 

In a `renderUpdateForm()` function in `book.js` there is a hidden field called `quote_count`, with a counter that is set up to loop through the array of quotes and count as each quote is rendered to the DOM. The counter is also attached to the attribute of `id` for `input-quote` so that when viewing the information 
in the elements tab on the console in the edit-form, each quote using string interpolation now has an id of `input-quote1` or `input-quote2` and so on, as the 
counter loops through this array. 

```
    let counter = 0
    this.quotes.forEach(quote_info => {
      counter +=1;
      html_string = html_string + `<textarea id="input-quote${counter}" name="quote${quote_info.id}" data-quoteid="${quote_info.id}" rows="3" class="form-control">` + quote_info.quote + `</textarea> <br>`
    });

    html_string = html_string + `<input id='quote_count' type="hidden" name="quote_count" value="${counter}">`
```

<img width="656" alt="Screen Shot 2021-03-04 at 2 42 51 PM" src="https://user-images.githubusercontent.com/26771302/110021059-48337e80-7cf8-11eb-8297-a687c466ed0a.png">

That counter is then also used within the `updateFormHandler()` function, a function used in order to grab all of the information from the updated book, and has an Event Listener attached to it so that whenever a user 'clicks' on the save book button, all updated information is saved to the database, and in the DOM. 

```
  function updateFormHandler(e) {
    e.preventDefault();
    const id = e.target.dataset.id;
    const book = Book.findById(id);
    const title = e.target.querySelector("#input-title").value;
    const author = e.target.querySelector("#input-author").value;
    const summary = e.target.querySelector("#input-summary").value;


    const quoteCount = e.target.querySelector("#quote_count").value;
    const newQuotes = [];
    for (let i=1; i <= quoteCount; i++) {
      const quoteId = e.target.querySelector("#input-quote"+i).dataset.quoteid;
      const quoteValue = e.target.querySelector("#input-quote"+i).value;
      const quote = {
        id: quoteId,
        quote: quoteValue
      }
      newQuotes.push(quote)
    }
    console.log(newQuotes)

    patchBook(book, title, author, summary, newQuotes)
  }
```

By creating an empty array constant of `newQuotes`, and selecting the value of the hidden `quote_count` id, all of the quotes can be looped through, checked
that so long as they are less than or equal to the `quote_count`, and then individually selected by both the id, and the value of the quote (the text itself). 
Then each hash of quote information can be popped into the `newQuotes` array, and then patched through the `patchBook` function which will them make the `fetch` call and send all of this information from the frontend to the backend, and a book, and all of its quotes can be updated. The benefit of creating all new quotes and placing them into an array of hashes, is that rather than calling things individually and passing them through manually as was done in the `postFetch()` function for the initial five quotes, is that all these `newQuotes` can just be passed through the `patchBook()` function as a single array of hashes like so:

```
  function patchBook(book, title, author, summary, newQuotes) {
    console.log(title, author, summary, newQuotes)
    fetch(`http://localhost:3000/api/v1/books/${book.id}`, {
      method: 'PATCH',
      headers: {
        'Content-Type' : 'application/json',
        Accept: 'application/json',
      },
      body: JSON.stringify({
        title: title,
        author: author,
        summary: summary,
        quotes_attributes: newQuotes

      })
    })
    .then(res => res.json())
    .then(updatedBook => console.log(updatedBook));

  }
```


# Deleting and what a bugger it is. 
I had already completed MVP for the project, and JavaScript has felt like an enormous headache for me from start to finish. The gap between where my knowledge of it is, and application is vast. But that is the life of a coder, and in a constant search for the answers I do hope one day I feel on much stronger footing in terms of where my capabilities lay. 

Because of how the code was structured, and how once the `DOMContentLoaded()`, and how edit buttons are dynamically added to the page, putting an event listener on it directly can be tricky. The same thing goes with a delete button. Originally how things were written, the event listener is placed on the parent element, the `<div>` and then rendered down to the children. When trying to do so for delete however, and trying to place an event listener on the delete button itself following the same pattern, and then console.log out the data, I was met with the dreaded `undefined undefined undefined undefined undefined`. When trying to work around that by calling things by it's id to then pull all that data out of the database and manipulate it in the DOM, again I came up against a bit of a brick wall.

Discussing this problem with a fellow coding friend, there is always a solution, just as there is always 100 ways to write the same code, but it meant rejigging my code as is. 

A JavaScript promise is an object that may produce a single value some time in the future. It can be in a state of either fulfilled, rejected, or pending. As soon as you invoke one, it'll start to do the work. JavaScript is eager. It wants to start doing the work immediately even if all the information is not there when called. As JavaScript is synchronous by default, and single threaded, it wants to execute one block of code at a time, and won't do anything else until that first one is finished before moving on. But by using AJAX calls, and fetch, we try to work around that and create asynchronous code, so that a page doesn't have to be reloaded as data is sent back and forth. The fetch function retrieves data, and within our first `.then()` we capture our object, parse it into JSON or text, return this JSON-ified response. Then, in our next method call, inside of our second, `.then()` we go on to manipulate the data inside of this second callback function, and the DOM using all the info we received from fetch. 

This is what we've been asked to do with the portfolio project, maniupulating all of that lovely data within our AJAX calls, and showing it off on the page. But sometimes it's hard to manipulate the data on the page as is, and to place things like an Event Listener onto an attribute that technically does not exist yet.  

Enter `Async/Await` which are promises but with some syntactic sugar. Instead of chaining things together within the `.then()` and it's callbacks, it makes our code look as if it's behaving as if it's synchronous, and in a much more elegant fashion. 

Placing the word `async` before a function means that a function will always return a promise. Using the keyword of `await` means that Javascript waits until a promise is settled, before it returns the result. It means getting to avoid chaining things together within callbacks and using `.then()` to create an anonymous function to handle our result. You also get to avoid nesting your code. 

My original code calls the function `getBooks()` when the DOM loads, rendering out each individual book card to `#book-container` in the html. It also places event Listeners on the events like clicking on a new book, or updating the book, which subsequently calls the functions that will then render this data out to the DOM. 

```
document.addEventListener('DOMContentLoaded', () => {
  getBooks()

  const createBookForm = document.querySelector("#create-book-form");

  createBookForm.addEventListener("submit", (e) => createFormHandler(e))

  const bookContainer = document.querySelector('#book-container')
  bookContainer.addEventListener('click', e => {
    const id = e.target.dataset.id;
    const book = Book.findById(id);
    document.querySelector('#update-book').innerHTML = book.renderUpdateForm();
    document.querySelector('#new-quote').innerHTML = book.renderNewQuote();
  });

  document.querySelector('#update-book').addEventListener('submit', e => updateFormHandler(e))
  document.querySelector('#new-quote').addEventListener('submit', e => newQuoteHandler(e))

})

function getBooks() {
  fetch(endPoint)
  .then(response => response.json())
  .then(books => {
    books.data.forEach(book => {
      const newBook = new Book(book.id, book.attributes);
      document.querySelector('#book-container').innerHTML += newBook.renderBookCard();

    })
  })
}
```
Switching over to async and await, means that it's possible to wait until all of the books are fetched in the `getBooks()` function, before attaching events to each card so that the code doesn't get stuck. Because the way that my code is written, the actual delete button is not created by the code until after the AJAX call returned `bookData`.

```
document.addEventListener('DOMContentLoaded', async () => {
  await getBooks()

  document.querySelector("#create-book-form").addEventListener("submit", (e) => createFormHandler(e))
  document.querySelector('#update-book').addEventListener('submit', e => updateFormHandler(e))
  document.querySelector('#new-quote').addEventListener('submit', e => newQuoteHandler(e))
  document.querySelectorAll('.book-delete-button').forEach(item => { item.addEventListener('click', e => deleteBook(e)) })
  document.querySelectorAll('.book-edit-button').forEach(item => { item.addEventListener('click', e => editBook(e)) })


})

async function getBooks() {
  const response = await fetch(endPoint)
  const books = await response.json()
    books.data.forEach(book => {
      const newBook = new Book(book.id, book.attributes);
      document.querySelector('#book-container').innerHTML += newBook.renderBookCard();
    })
}
```

By writing two new functions, `deleteBook()` and `editBook()` it's also possible to move some of the logic out of the original event listener and into the functions themselves.

```
  async function deleteBook(e) {
    const bookId = e.srcElement.dataset.id;
    const response = await fetch(`http://localhost:3000/api/v1/books/${bookId}`, {
      method: 'DELETE',
      headers: {
        'Content-type': 'application/json',
        'Accept': 'application/json'
      }
    })
    const resp = await response.json();
    const book = Book.findById(bookId);
    book.remove();
    console.log('removed');
  }

  function editBook(e) {
    const id = e.srcElement.dataset.id;
    const book = Book.findById(id);
    document.querySelector('#update-book').innerHTML = book.renderUpdateForm();
    document.querySelector('#new-quote').innerHTML = book.renderNewQuote();
  }
```
The result is that the code looks cleaner, it makes more sense to me at least, than figuring out how to encapsulate the logic with the previous method in the event listener. And also that both delete and edit now work. 

At the moment however, even though it works, I'm not entirely certain _how_ it works. For the time being, I am leaving all this latter logic on a different branch in my github repository and not pushing up to the master branch. I would like to have a better understanding of it, before discussing it, but who knows, maybe after moving onto React these things will make more sense so that by the time I can schedule my portfolio review, I'll feel confident enough to talk about it. 

If you've stayed with me to the end, thank you. Learning this has been frustrating and enlightening, but thankfully I've kept all my hair and have not thrown the entire computer out, as was suggested by fellow students in debugging sessions earlier on in the project. 
