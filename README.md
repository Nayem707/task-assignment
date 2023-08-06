## Task-assignment

##### To fetch posts from a Node.js backend that have been created or updated within the last 7 days using Redux or Redux Toolkit, we need to follow these steps:

### Set up the Backend:

First, ensure that you have a Node.js backend with MongoDB and Mongoose set up. Define a schema and model for the posts in Mongoose, which should include a field to store the post's creation and last update timestamps.

### Create Redux Actions:

In the frontend application, create Redux actions to handle fetching the posts. We'll need at least three actions: one to initiate the request, one to handle successful response, and another to handle any errors.

### Create a Redux Thunk:

Use Redux Thunk middleware to create an asynchronous action to fetch the posts. The asynchronous action will call the backend API to retrieve the posts that meet the criteria of being created or updated within the last 7 days.

### Connect Redux to React Component:

Connect the Redux store to your React component to access the fetched posts.

Here's a general outline of the code structure:

### Backend Setup:

Set up the Node.js backend with MongoDB and Mongoose. Define a Post schema with a createdAt and updatedAt field for timestamps.

```js
// Example Post Schema using Mongoose
const mongoose = require('mongoose');

const postSchema = new mongoose.Schema({
  title: { type: String, required: true },
  content: { type: String, required: true },
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now },
});

const Post = mongoose.model('Post', postSchema);

module.exports = Post;
```

###### Redux Actions and Thunk: In your Redux code, create actions and a thunk to fetch the posts from the backend.

```js
// Redux Actions
const FETCH_POSTS_REQUEST = 'FETCH_POSTS_REQUEST';
const FETCH_POSTS_SUCCESS = 'FETCH_POSTS_SUCCESS';
const FETCH_POSTS_FAILURE = 'FETCH_POSTS_FAILURE';

// Action Creators
const fetchPostsRequest = () => ({ type: FETCH_POSTS_REQUEST });
const fetchPostsSuccess = (posts) => ({
  type: FETCH_POSTS_SUCCESS,
  payload: posts,
});
const fetchPostsFailure = (error) => ({
  type: FETCH_POSTS_FAILURE,
  payload: error,
});

// Thunk to fetch posts from the backend
const fetchPosts = () => async (dispatch) => {
  try {
    dispatch(fetchPostsRequest());
    // Make an API call to the backend to fetch posts
    const response = await fetch('/api/posts'); // Replace with your API endpoint
    if (!response.ok) {
      throw new Error('Failed to fetch posts');
    }
    const posts = await response.json();
    dispatch(fetchPostsSuccess(posts));
  } catch (error) {
    dispatch(fetchPostsFailure(error.message));
  }
};
```

###### Redux Reducer: Create a Redux reducer to handle the state changes based on the fetch posts actions.

```js
const initialState = {
  posts: [],
  loading: false,
  error: null,
};

const postsReducer = (state = initialState, action) => {
  switch (action.type) {
    case FETCH_POSTS_REQUEST:
      return { ...state, loading: true, error: null };
    case FETCH_POSTS_SUCCESS:
      return { ...state, loading: false, posts: action.payload, error: null };
    case FETCH_POSTS_FAILURE:
      return { ...state, loading: false, error: action.payload };
    default:
      return state;
  }
};
```

###### Connect Redux to React Component: Connect the Redux store to your React component to access the fetched posts.

```js
import { useSelector, useDispatch } from 'react-redux';
import { useEffect } from 'react';
import { fetchPosts } from './redux/posts';

const PostList = () => {
  const dispatch = useDispatch();
  const { posts, loading, error } = useSelector((state) => state.posts);

  useEffect(() => {
    dispatch(fetchPosts());
  }, [dispatch]);

  if (loading) {
    return <div>Loading...</div>;
  }

  if (error) {
    return <div>Error: {error}</div>;
  }

  return (
    <div>
      <h1>Recent Posts</h1>
      <ul>
        {posts.map((post) => (
          <li key={post._id}>
            <h2>{post.title}</h2>
            <p>{post.content}</p>
          </li>
        ))}
      </ul>
    </div>
  );
};
export default PostList;
```

With this approach, your React component PostList will display the list of posts fetched from the backend, and Redux will handle the state management of the posts data, loading state, and any potential errors that may occur during the API call.

Please note that the backend code, including the API endpoint for fetching posts, is not included here, as it depends on your specific backend implementation. Similarly, you need to set up the Redux store and combine the postsReducer with other reducers in the application.
