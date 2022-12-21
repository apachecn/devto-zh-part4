# Custom onChange in React Final Form

> 原文：[https://dev.to/doxomo/custom-onchange-in-react-final-form-4j09](https://dev.to/doxomo/custom-onchange-in-react-final-form-4j09)

Let's take a case when we need to call our own function to change a state of a specific field in a form (our react final form).

Here is a way to do the exact that:
You should pass your function as a parameter and after that just call it inside of the onChange method

Example:

```
const FormGroupAdapter = ({ input, inputOnChange }) => {
    const inputProps = {
        ...input,
        onChange: e => {
            input.onChange(e);
            inputOnChange && inputOnChange(e);
        }
    };

    return <input {...inputProps} />; };

const handleChange = event => {
    console.log("!!!", event.target.value);
};

const App = () => (
    <Form
        ...
        render={({ handleSubmit, reset, submitting, pristine, values }) => (
            <form onSubmit={handleSubmit}>
                <div>
                    <label>some label</label>
                    <Field
                        name="someField"
                        component={FormGroupAdapter}
                        inputOnChange={handleChange}
                    />
                </div>
                ...
            </form>
        )}
    /> ); 
```

[Live example](https://codesandbox.io/s/nk63v7615j)