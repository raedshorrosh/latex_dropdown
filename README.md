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
In your STACK question, define the Maxima variables that power the component. You must stringify them into JSON using STACK's `stackjson_stringify` function.

**Example Maxima Code:**
```maxima
/* The items format is:
   [ Title, [Options...], Correct_Position (1-based index), Type, Weight ] */
items: [
    ["החומר המחמצן", ["KMnO4", "HCl", "MnCl2", "H2O"], 1, "dropdown", 1],
    ["החומר המחזר", ["KMnO4", "HCl", "MnCl2", "H2O"], 2, "dropdown", 1],
    ["תוצר החמצון", ["MnCl2", "H2O", "KCl", "Cl2"], 4, "dropdown", 1],
    ["תוצר החיזור", ["MnCl2", "H2O", "KCl", "Cl2"], 1, "dropdown", 1],
    ["מספר מולי אלקטרונים", ["10"], 1, "input", 1]
];

js_items: stackjson_stringify(items);

/* The name of the STACK input variable. 
   Defaults to "ans1" if not provided, but you can change it here 
   if you have multiple components on the same page. */
js_input_ans: stackjson_stringify("ans1");

/* Define the Teacher Answer (ta) list to use as the model answer.
   The list contains the answers array (1-based index for dropdowns, exact text for inputs)
   and the normalized grade 1. */
ta: [ [1, 2, 4, 1, "10"], 1 ];
```

## Step 3: Paste the HTML into your Question Text
1. Open the **`SINGLE_FILE_FOR_STACK.html`** file that was generated in Step 1.
2. Copy its entire contents.
3. Paste everything directly into your STACK **Question text** field. That's it!
*(Note: The file uses version-locked CDNs to load React and KaTeX, making the text incredibly lightweight and taking advantage of browser caching so your quizzes load instantly.)*

## Step 4: Configure the Input in STACK
Scroll down to the **Input: ans1** section in your STACK question settings and configure it as follows:
- **Input type**: `String`
- **Model answer**: `stackjson_stringify(ta)` *(This evaluates the Teacher Answer list defined in your question variables)*
- **Student must verify**: `No`
- **Show the validation**: `No`

*Important to Hide the Input:* Since the React app handles the UI, you should hide the default STACK input box. In your Question Text (from Step 3), wrap the input tags in a hidden div like this:
```html
<div style="display:none;">
    [[input:ans1]] [[validation:ans1]]
</div>
```

## Step 5: Setup PRT (Grading)
The React component normalizes the grade to a number between `0` and `1` and outputs it alongside the answers as a JSON array string. In STACK, you must parse this JSON string back into a Maxima list before you can grade it.

In your **Potential response tree (PRT)**, scroll down to the **Feedback variables** and add this line:
```maxima
parsed_ans1: stackjson_parse(ans1);
```
- `parsed_ans1[1]` will be the Maxima list of student answers.
- `parsed_ans1[2]` will be the calculated numeric grade (0 to 1).

You can now configure your PRT node to evaluate this grade directly:
- **Answer Test**: `AlgEquiv`
- **SAns**: `parsed_ans1[2]`
- **TAns**: `1`
- **Node 1 True feedback**: 
  - Score: `1`
- **Node 1 False feedback**: 
  - Score: `parsed_ans1[2]` *(This assigns the partial credit calculated by the React app)*

