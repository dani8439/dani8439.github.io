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
like editing a collection: 

```        case 'EDIT_COLLECTION':
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
unnesting is the sane thing to do when it comes to routes. While my `CollectionReducer` doesn't really look that interesting on the whole, 

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

Life is so much simpler having `Collections` and `Pieces` as completely separate routes. 
