```
const Logic = () => {
  const [active, setActive] = useState(false);
  return (
    <div className="logic-container">
      <button onClick={() => {setActive(!active);}}>Show</button>
      <div className={`content ${active ? "visible" : "hidden"}`}>
        <BookList />
      </div>
    </div>
  );
};

```
you can show a component by logical


[Back to Home](../../)

[1. extensions](../extensions/)

[2. setup-react](../2-setup-react/)

[3. children-props](../3-children-props/)

4. condition-to-show

[5. data.js](../5-data.js/)

[6. form](../6-form/)