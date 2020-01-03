# redux-reducers-switch-vs-table
When it comes to large-scale applications, which approach is preferrable?

### Switch

```js
export default function FormReducer(state = initialState(), action) {
    const { type } = action;
    switch (type) {
        case 'HANDLE_CHANGE':
            let { key, value } = action;
            return {
                ...state,
                userInputs: {
                    ...state.userInputs,
                    [key]: value
                }
            };
        case 'GET_QUESTIONS':
            let { questionList } = action;
            return {
                ...state,
                questionList
            };
        case 'HANDLE_CONTINUE':
            return defineNewQuestions(state, action);
        case 'REMOVE_QUESTIONS':
            return {
                ...state,
                questionList: []
            };
        default:
            return state;
    }
}
```

### Table

```js
export default function FormReducer(state = initialState(), action) {
    const currentCase = cases.get(action.type);
    const output = currentCase ? currentCase(action, state) : state;
    return output;
}
```

### Case Registration System

```js
export const cases = new Map();
function handleChange(action, state) {
    let { key, value } = action;
    return {
        ...state,
        userInputs: {
            ...state.userInputs,
            [key]: value
        }
    };
}
function getQuestions(action, state) {
    let { questionList } = action;
    return {
        ...state,
        questionList
    };
}
function removeQuestions(_action, state) {
    return {
        ...state,
        questionList: []
    };
}
const actions = [
    ['HANDLE_CHANGE', handleChange],
    ['GET_QUESTIONS', getQuestions],
    ['REMOVE_QUESTIONS', removeQuestions],
    ['HANDLE_CONTINUE', defineNewQuestions]
];
function registerCases(actionList) {
    console.log('registering cases');
    actionList.forEach(action => cases.set(action[0], action[1]));
}
registerCases(actions);
export const registerCase = action => {
    const hasCollision = cases.get(action[0]) ? true : false;
    if (hasCollision) {
        console.warn(`Duplicate case name: ${action[0]}. The original case is overwritten.`);
    }
    cases.set(action[0], action[1]);
};
```
