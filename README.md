# How to Use the Math Dropdown Component in STACK

This project uses React and KaTeX, which means it needs to be "compiled" before it can be used in Moodle. 
We have set it up to generate a single, lightweight HTML file that you can paste directly into Moodle—no external servers required!

Follow these steps to deploy this to your STACK questions.

## Step 1: Export and Build the Code
1. Export this project from AI Studio (download as ZIP).
2. Extract the ZIP to your computer.
3. Open your terminal in that folder and run:
   ```bash
   npm install
   npm run build
   ```
4. Look in the root of the project folder. You will find a newly generated file called **`SINGLE_FILE_FOR_STACK.html`**. 

## Step 2: Setup your STACK Question Variables
In your STACK question, define the Maxima variables that power the component. You must stringify them into JSON using STACK's `stack_json_stringify` function.

**Example Maxima Code:**
```maxima
/* The correct answers in order */
tas: ["KMnO4", "HCl", "MnCl2", "H2O", "10"];
js_tas: stack_json_stringify(tas);

/* The dropdown options. 
   Pass a 2D array to give different options to different dropdowns. 
   Pass an empty list [] for inputs. */
options: [
    [ ["id", "HCl"], ["latex", "HCl"] ],  
    [ ["id", "KMnO4"], ["latex", "KMnO_4"] ],
    /* ... add options for the rest of your dropdowns here ... */
    [] /* Empty list for the text input */
];
js_options: stack_json_stringify(options);

/* The labels for the right side (Hebrew RTL) */
titles: ["החומר המחמצן", "החומר המחזר", "תוצר החמצון", "תוצר החיזור", "מספר מולי אלקטרונים"];
js_titles: stack_json_stringify(titles);

/* The type of input for each row ("dropdown" or "input") */
types: ["dropdown", "dropdown", "dropdown", "dropdown", "input"];
js_types: stack_json_stringify(types);

/* The weights for each input. 
   If not provided, the component will assume all inputs have an equal weight of 1. */
weights: [1, 1, 1, 1, 1];
js_weights: stack_json_stringify(weights);

/* The names of the STACK input variables. 
   Defaults to "ans1" if not provided, but you can change it here 
   if you have multiple components on the same page. */
js_input_ans: stack_json_stringify("ans1");
```

## Step 3: Paste the HTML into your Question Text
1. Open the **`SINGLE_FILE_FOR_STACK.html`** file that was generated in Step 1.
2. Copy its entire contents.
3. Paste everything directly into your STACK **Question text** field. That's it!

*(Note: The file uses version-locked CDNs to load React and KaTeX, making the text incredibly lightweight and taking advantage of browser caching so your quizzes load instantly.)*

## Step 4: Configure the Input in STACK
Scroll down to the **Input: ans1** section in your STACK question settings and configure it as follows:
- **Input type**: `String`
- **Model answer**: `stack_json_stringify([tas, 1])` *(This tells STACK what the perfect JSON output looks like)*
- **Student must verify**: `No`
- **Show the validation**: `No`

*Important to Hide the Input:* Since the React app handles the UI, you should hide the default STACK input box. In your Question Text (from Step 3), wrap the input tags in a hidden div like this:
```html
<div style="display:none;">
    [[input:ans1]] [[validation:ans1]]
</div>
```

## Step 5: Setup PRT (Grading)
The React component automatically normalizes the grade to a number between `0` and `1` and includes it in the JSON string. Maxima will automatically parse this JSON into a list. 
- `ans1[1]` will be the list of student answers.
- `ans1[2]` will be the calculated numeric grade (0 to 1).

Configure your **Potential response tree (PRT)** node as follows to apply this grade:
- **Answer Test**: `AlgEquiv`
- **SAns**: `ans1[2]`
- **TAns**: `1`
- **Node 1 True feedback**: 
  - Score: `1`
- **Node 1 False feedback**: 
  - Score: `ans1[2]` *(This assigns the partial credit calculated by the React app)*

