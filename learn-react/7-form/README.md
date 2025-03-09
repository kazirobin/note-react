at first i create a database useState

```
 const [database, setDatabase] = useState([]);

```

then set data object use another state

```
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    password: "",
  });
```

handle input box changes

```
    // Handle input changes
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData({ ...formData, [name]: value }); // Update form data state
  };
```

handle from by click submit

```
  // Handle form submission
  const handleSubmit = (e) => {
    e.preventDefault(); // Prevent default form submission

    if (formData.name && formData.email && formData.password) {
      setDatabase([...database, formData]); // Add new entry to the database

      setFormData({ name: "", email: "", password: "" }); // Clear form fields

      console.log("Database:", database); // Log the updated database

    } else {
      alert("Please fill in all fields.");
    }
  };
```

show current data

```
{data.map((user)=>{return(<div>
    <b>{user.name}</b>
    <b>{user.email}</b>
    <b>{user.password}</b>
    </div>)})}
```
[Back to Home](../../)

[1. extensions](../extensions/)

[2. setup-react](../2-setup-react/)

[3. children-props](../3-children-props/)

[4. children-props](../4-condition-to-show/)

[5. data.js](../5-data.js/)

6. form