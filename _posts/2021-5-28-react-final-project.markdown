# Well it's finally here. 

The final final project. 

For my capstone project at the Flatiron School, I wanted to revisit a previous project that I had built for the Rails section, but now with the skills 
I've learned as a more well rounded coder.  

Pottery Keeper is an extension of Collection Tracker, a way for anyone to keep track of large collections of pieces. It could just as easily be rejigged to model 
trains, LEGO pieces, or anything that someone collects and has a great many items of. 

Built with a Rails API backend, it's fairly simple and straightforward with two models, `Collection` and `Pieces`, and basic CRUD capabilities (Create, Read, Edit, Update
and Delete). Down the road, I would like for multiple users to be able to use the App, and implementing a `User` model should be fairly straightforward. 

## Let's get Coding. 
After the turmoil that was JavaScript, learning React and Redux seemed much more straightforward. Sometimes. As what usually happens with me, most of my learning 
happens in project mode, when getting down to the nitty-gritty, not entirely certain where my ambition outstrips my skills. 

At first I built my application while watching the Expense Tracker videos by Annabel. They were definitely very clear, and I had a surprisingly lack of bugs, until
I got to the point of wanting to do basic editing capabilities on any instance of the `Piece` class. Because of how the videos were layed out, I initially had 
nested routes of `http:localhost3000/api/v1/collections` and `http:localhost3000/api/v1/collections/{collection.id}/pieces/{piece.id}`. And when it came down to 
trying to edit a piece, creating a reducer for such a nested piece of data was an enormous headache. Rather than having a fairly shallow reducer as I had with something
like editing a collection. 


```
case 'EDIT_COLLECTION':
  let collectionsThree = state.collections.map(collection => {
    if (collection.id === action.payload.id) {
      return action.payload
    } else {
      return collection
    }
   })
  return {...state, collections: collectionsThree}
```


It grew and grew, first trying to find the collection. Then finding the associated pieces. Then finding the particular piece I wanted to update. Thankfully, in 
Open Office Hours for the project, the always helpful Dakota said that the reason why doing such a thing was such a pain, was because most people avoided it. By
unnesting my routes, I could do away with a lot of headaches, and have my reducers be much more shallow, and not feel like I was picking through a needle in a haystack
in order to manipulate and send back and forth the right information from frontend to backend. And after a few days of debugging and untangling my code, as it turned out
unnesting is the sane thing to do when it comes to routes. While my `CollectionReducer` doesn't really look that interesting on the whole. 


```
export default function collectionReducer(state = {collections: []}, action) {
    switch (action.type) {
        case 'FETCH_COLLECTIONS':
            return {collections: action.payload}
        case 'ADD_COLLECTION':
            return {...state, collections: [...state.collections, action.payload] }
        case 'ADD_PIECE':
            let collections = state.collections.map(collection => {
                if (collection.id === action.payload.id) {
                    return action.payload
                } else {
                    return collection
                }
            })
            return {...state, collections: collections}
        case 'DELETE_PIECE':
            let collectionsTwo = state.collections.map(collection => {
                if (collection.id === action.payload.id) {
                    return action.payload
                } else {
                    return collection
                }
            })
            return {...state, collections: collectionsTwo}
        case 'EDIT_COLLECTION':
            let collectionsThree = state.collections.map(collection => {
                if (collection.id === action.payload.id) {
                    return action.payload
                } else {
                    return collection
                }
            })
            return {...state, collections: collectionsThree}
        case 'EDIT_PIECE': 
            let collectionsFour = state.collections.map(collection => {
                if (collection.id === action.payload.id) {
                    return action.payload 
                } else {
                    return collection
                }
            })
            return {...state, collections: collectionsFour}        

        default: 
            return state 
    }
}
```

Life is so much simpler having `Collections` and `Pieces` as completely separate routes. I will avoid nesting in the future if I can avoid it. 

## Onto the interesting stuff 

Like so many others, struggling to grasp the concepts of React, Redux, thunk, Middleware, state, props and the like, drove me a little batty. 

One particular thing that I wanted for a user to be able to do, was to click on an edit form, and have the edit form pre-filled in with all the information of a particular collection, or piece that they were editing. Using a Class Component, that hooks into the state in order to manipulate it in PieceEdit, after talking with a few people, and reading documentation, I was able to use both `componentDidMount` and optional chaining. `componentDidMount` fires when the page is loaded, and at that point, sets the state of my attributes. By using optional chaining, it checks to see if a particular attribute exists, and if it exists, sets the state to that. Same thing goes inside of the render itself of each edit card, so that *if* a `pattern_name` or `quantity` exists, the form is pre-filled out with that information. 

Then, as a person fills out the form, `handleChange` hooks into the state, altering the props as a person types. `handleSubmit` then sets the edited piece to this new state, calls the `editPiece` action which makes a call to the backend and the CRUD actions to update the database. Buried within the `handleSubmit` function, is also a method called `onSave`. `onSave` is passed down from PieceContainer.js, and is assigned to a function called `handleEdit`. In PieceContainer.js lives the function `handleEdit`, which sets the state of the `pieceToBeEdited` to the current `piece` that has been clicked. Within the render of `PieceEdit` there is a conditional render. If there's no piece to be edited, a user sees the form on the page to add a new piece. But, if there is a piece to be edited, instead, they see an edit form. The prop of the `pieceToBeEdited` is passed down. Then the `hideEdit` function toggles between a boolean of true and false. When a user clicks on the submit button within PieceEdit, the button then toggles to false, and the form disappears. 

The code for both components is below: 

PieceEdit.js

```
import React from 'react';
import { connect } from 'react-redux';
import { editPiece } from '../actions/editPiece'

class PieceEdit extends React.Component {
    state = {
        collection_id: '',
        piece_name: '',
        pattern_name: '',
        quantity: '',
        image_url: ''
    }


    componentDidMount() {
        this.setState({
            collection_id: this.props.piece?.collection_id,
            piece_name: this.props.piece?.piece_name,
            pattern_name: this.props.piece?.pattern_name,
            quantity: this.props.piece?.quantity,
            image_url: this.props.piece?.image_url

        })
    }

    handleChange = (event) => {
        this.setState({
            [event.target.name]: event.target.value 
        })

    }

    handleSubmit = (event) => {
        event.preventDefault();
        let piece = {...this.state, id: this.props.piece.id}
        this.props.editPiece(piece)
        this.props.onSave();

    }


    render() {
        return (
            <div className="edit-card"> 
                <h1>Edit Piece</h1>
                <form onSubmit={this.handleSubmit}>
                <label>Piece Name:</label>
                    <input type="text" placeholder="Piece Name" name="piece_name" defaultValue={this.props.piece?.piece_name} onChange={this.handleChange}/><br></br>
                    <br></br>
                    <label>Pattern Name:</label>
                    <input type="text" placeholder="Pattern Name" name="pattern_name" defaultValue={this.props.piece?.pattern_name} onChange={this.handleChange}/><br></br>
                    <br></br>
                    <label>Image:</label>
                    <input type="text" placeholder="Image Url" name="image_url" defaultValue={this.props.piece?.image_url} onChange={this.handleChange}/><br></br>
                    <br></br>
                    <label>Quantity:</label>
                    <input type="text" placeholder="Quantity" name="quantity" defaultValue={this.props.piece?.quantity} onChange={this.handleChange}/><br></br>
                    <br></br>
                    <input type="submit" value="Edit This Piece"></input>
                </form>
            </div>

        )
    }
}
export default connect(null, {editPiece})(PieceEdit);
```

PieceContainer.js

```
import React from 'react';
import PieceInput from '../components/PieceInput'
import Pieces from '../components/Pieces'
import PieceEdit from '../components/PieceEdit'

class PiecesContainer extends React.Component {
    state = {
        pieceToBeEdited: null
    }

    handleEdit = (piece) => {
        // console.log(piece)
        this.setState({
            pieceToBeEdited: piece
        })
    }

    hideEdit = () => {
        console.log("hide Me")
        this.setState({
            pieceToBeEdited: null
        })
    }

    render() {
        return (
            <div>
                {
                    !this.state.pieceToBeEdited && <PieceInput collection={this.props.collection}/>
                }
                {
                    this.state.pieceToBeEdited && <PieceEdit piece={this.state.pieceToBeEdited} onSave={this.hideEdit} />
                }
                <Pieces pieces={this.props.collection && this.props.collection.pieces} onEdit={this.handleEdit} />

            </div>
        )
    }
}
export default PiecesContainer 
```

And when on the webpage itself a user will see either this:
 

<img width="1381" alt="Screen Shot 2021-05-28 at 4 05 29 PM" src="https://user-images.githubusercontent.com/26771302/120036993-ede04c00-bfce-11eb-9497-1302e9a487bb.png">

Or this, depending on the truthy or falsy value of `hideEdit`:

<img width="1405" alt="Screen Shot 2021-05-28 at 4 05 42 PM" src="https://user-images.githubusercontent.com/26771302/120037048-018bb280-bfcf-11eb-9c0b-6cdd03132f19.png">

I used a similar logic, toggling between truthy and falsy within the Collection.js Component as well, so that an edit form appears to edit a collection when the edit button is clicked. It's written a little bit differently, as Collection.js is a functional component and not a class component, and I dabbled a little bit with my first hook `useState`. 

If there's anything that this project has taught me, it's that this is just the tip of the iceberg in terms of React's capabilities. It really can do so much. While a lot of patterns seem much clearer, I of course feel like I am just scratching the surface in terms of what I can learn to do. Which I hope to do, even though my journey at Flatiron is coming to a close, and I'll be moving onto the unknown. 

All in all, I'm fairly pleased with how this project turned out, and am amazed it's the final one as part of my time here. I came into Flatiron knowing absolutely nothing about coding whatsoever, and have come out with so many new skills, that it can be hard to be down on myself for what I don't know. Instead, I want to look at it as all the things I have learned and can do. And hopefully this is just the beginning of learning more. 
