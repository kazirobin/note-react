```
import React from 'react'
const data = [
    {
        title:"title 1",
        details:"details 1",
        text:"this is text 1"
    },
    {
        title:"title 2",
        details:"details 2",
        text:"this is text 2"
    },
    {
        title:"title 3",
        details:"details 3",
        text:"this is text 3"
    }
]
const BookList = () => {
  return (
    <div>BookList
        <Book data={data[0]}>
            <p>hello</p>
        </Book>
        <Book data={data[1]}/>
        <Book data={data[2]}/>
    </div>
  )
}
const Book = ({data,children}) => {
    const {title,details,text} = data
    return (
        <div>
            <p>{children}</p>
            <h3>{title}</h3>
        </div>
    )
 }
export default BookList
```

```
export const News = () => {
  return (
    <>
      <h1>hello news</h1>
    </>
  );
};
```

here i just create a simple News component
with export it in anywhere.

props is useful for passing data from parent to children

```
<Book data={data[2]}/>
```

if you want to get data from children then pass a function
and get data by parameter

```
<button onClick={() => handleUpdate(data)}>Update</button>
```

[Back to Home](../../)

[1. extensions](../extensions/)

[2. setup-react](../2-setup-react/)

3. children-props

[4. condition-to-show](../4-condition-to-show/)

[5. data.js](../5-data.js/)

[6. form](../6-form/)
