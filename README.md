The file app.svelte when used in VSCode produces a lot of errors that are not really errors.



To reproduce:
1. pnpm install
2. make sure biome is installed globally.
3. run vsCode in the top folder with the biome extension enabled.
4. Open src/app.svelte and see the errors in the UI and the Problems tab.

I included the output from the CLI (cli.log) and the errors from the VSCode Problems (vscode-errors.log).

All reproduced here as well:

<details>
<summary>Svelte File</summary>

```svelte
<script lang="ts">
import TasksForm from './components/tasks-form.svelte';
import TasksList from './components/tasks-list.svelte';
import type { Filter, Task } from './types';

const message = 'Tasks App';
let currentFilter = $state<Filter>('all');
let tasks = $state<Task[]>([]);
let totalDone = $derived(tasks.reduce((total, task) => total + Number(task.done), 0));

const filteredTasks = $derived.by(() => {
  switch (currentFilter) {
    case 'all': {
      return tasks;
    }
    case 'done': {
      return tasks.filter((task) => task.done);
    }
    case 'todo': {
      return tasks.filter((task) => !task.done);
    }
  }
  return tasks;
});

function addTask(newTask: string) {
  tasks.push({
    id: crypto.randomUUID(),
    title: newTask,
    done: false,
  });
}

function toggleDone(task: Task) {
  task.done = !task.done;
}

function removeTask(id: string) {
  const index = tasks.findIndex((task) => task.id === id);
  tasks.splice(index, 1);
}
</script>

{#snippet filterButton(filter: Filter)}
  <button
    onclick={() => (currentFilter = filter)}
    class:contrast={currentFilter === filter}
    class="secondary filterButton"
    >{filter}</button
  >
{/snippet}

<main>
  <h1>{message}</h1>
  <TasksForm {addTask} />
  <p>
    {#if tasks.length}
      {totalDone} / {tasks.length} tasks completed
    {:else}
      Add a task to get started.
    {/if}
  </p>
  {#if tasks.length}
    <div class="button-container">
      {@render filterButton("all")}
      {@render filterButton("todo")}
      {@render filterButton("done")}
    </div>
  {/if}
  <TasksList
    tasks={filteredTasks}
    {toggleDone}
    {removeTask}
  />
</main>

<style>
  main {
    margin: 1rem auto;
    max-width: 800px;
  }

  .button-container {
    display: flex;
    justify-content: end;
    margin-bottom: 1rem;
    gap: 0.5rem;
  }

  .filterButton {
    text-transform: capitalize;
  }
</style>
```
</details>

<details>
<summary>CLI Errors</summary>

```
$ biome lint app.svelte
app.svelte:2:8 lint/correctness/noUnusedImports  FIXABLE  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ⚠ This import is unused.
  
    1 │ <script lang="ts">
  > 2 │ import TasksForm from './components/tasks-form.svelte';
      │        ^^^^^^^^^
    3 │ import TasksList from './components/tasks-list.svelte';
    4 │ import type { Filter, Task } from './types';
  
  ℹ Unused imports might be the result of an incomplete refactoring.
  
  ℹ Unsafe fix: Remove the unused imports.
  
    1 │ import·TasksForm·from·'./components/tasks-form.svelte';
      │ -------------------------------------------------------

app.svelte:3:8 lint/correctness/noUnusedImports  FIXABLE  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ⚠ This import is unused.
  
    1 │ <script lang="ts">
    2 │ import TasksForm from './components/tasks-form.svelte';
  > 3 │ import TasksList from './components/tasks-list.svelte';
      │        ^^^^^^^^^
    4 │ import type { Filter, Task } from './types';
    5 │ 
  
  ℹ Unused imports might be the result of an incomplete refactoring.
  
  ℹ Unsafe fix: Remove the unused imports.
  
     1  1 │   import TasksForm from './components/tasks-form.svelte';
     2    │ - import·TasksList·from·'./components/tasks-list.svelte';
     3  2 │   import type { Filter, Task } from './types';
     4  3 │   
  

app.svelte:6:7 lint/correctness/noUnusedVariables  FIXABLE  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ⚠ This variable message is unused.
  
    4 │ import type { Filter, Task } from './types';
    5 │ 
  > 6 │ const message = 'Tasks App';
      │       ^^^^^^^
    7 │ let currentFilter = $state<Filter>('all');
    8 │ let tasks = $state<Task[]>([]);
  
  ℹ Unused variables are often the result of an incomplete refactoring, typos, or other sources of bugs.
  
  ℹ Unsafe fix: If this is intentional, prepend message with an underscore.
  
     3  3 │   import type { Filter, Task } from './types';
     4  4 │   
     5    │ - const·message·=·'Tasks·App';
        5 │ + const·_message·=·'Tasks·App';
     6  6 │   let currentFilter = $state<Filter>('all');
     7  7 │   let tasks = $state<Task[]>([]);
  

app.svelte:9:5 lint/correctness/noUnusedVariables  FIXABLE  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ⚠ This variable totalDone is unused.
  
     7 │ let currentFilter = $state<Filter>('all');
     8 │ let tasks = $state<Task[]>([]);
   > 9 │ let totalDone = $derived(tasks.reduce((total, task) => total + Number(task.done), 0));
       │     ^^^^^^^^^
    10 │ 
    11 │ const filteredTasks = $derived.by(() => {
  
  ℹ Unused variables are often the result of an incomplete refactoring, typos, or other sources of bugs.
  
  ℹ Unsafe fix: If this is intentional, prepend totalDone with an underscore.
  
     6  6 │   let currentFilter = $state<Filter>('all');
     7  7 │   let tasks = $state<Task[]>([]);
     8    │ - let·totalDone·=·$derived(tasks.reduce((total,·task)·=>·total·+·Number(task.done),·0));
        8 │ + let·_totalDone·=·$derived(tasks.reduce((total,·task)·=>·total·+·Number(task.done),·0));
     9  9 │   
    10 10 │   const filteredTasks = $derived.by(() => {
  

app.svelte:11:7 lint/correctness/noUnusedVariables  FIXABLE  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ⚠ This variable filteredTasks is unused.
  
     9 │ let totalDone = $derived(tasks.reduce((total, task) => total + Number(task.done), 0));
    10 │ 
  > 11 │ const filteredTasks = $derived.by(() => {
       │       ^^^^^^^^^^^^^
    12 │   switch (currentFilter) {
    13 │     case 'all': {
  
  ℹ Unused variables are often the result of an incomplete refactoring, typos, or other sources of bugs.
  
  ℹ Unsafe fix: If this is intentional, prepend filteredTasks with an underscore.
  
     8  8 │   let totalDone = $derived(tasks.reduce((total, task) => total + Number(task.done), 0));
     9  9 │   
    10    │ - const·filteredTasks·=·$derived.by(()·=>·{
       10 │ + const·_filteredTasks·=·$derived.by(()·=>·{
    11 11 │     switch (currentFilter) {
    12 12 │       case 'all': {
  

app.svelte:26:10 lint/correctness/noUnusedVariables  FIXABLE  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ⚠ This function addTask is unused.
  
    24 │ });
    25 │ 
  > 26 │ function addTask(newTask: string) {
       │          ^^^^^^^
    27 │   tasks.push({
    28 │     id: crypto.randomUUID(),
  
  ℹ Unused variables are often the result of an incomplete refactoring, typos, or other sources of bugs.
  
  ℹ Unsafe fix: If this is intentional, prepend addTask with an underscore.
  
    23 23 │   });
    24 24 │   
    25    │ - function·addTask(newTask:·string)·{
       25 │ + function·_addTask(newTask:·string)·{
    26 26 │     tasks.push({
    27 27 │       id: crypto.randomUUID(),
  

app.svelte:34:10 lint/correctness/noUnusedVariables  FIXABLE  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ⚠ This function toggleDone is unused.
  
    32 │ }
    33 │ 
  > 34 │ function toggleDone(task: Task) {
       │          ^^^^^^^^^^
    35 │   task.done = !task.done;
    36 │ }
  
  ℹ Unused variables are often the result of an incomplete refactoring, typos, or other sources of bugs.
  
  ℹ Unsafe fix: If this is intentional, prepend toggleDone with an underscore.
  
    31 31 │   }
    32 32 │   
    33    │ - function·toggleDone(task:·Task)·{
       33 │ + function·_toggleDone(task:·Task)·{
    34 34 │     task.done = !task.done;
    35 35 │   }
  

app.svelte:38:10 lint/correctness/noUnusedVariables  FIXABLE  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ⚠ This function removeTask is unused.
  
    36 │ }
    37 │ 
  > 38 │ function removeTask(id: string) {
       │          ^^^^^^^^^^
    39 │   const index = tasks.findIndex((task) => task.id === id);
    40 │   tasks.splice(index, 1);
  
  ℹ Unused variables are often the result of an incomplete refactoring, typos, or other sources of bugs.
  
  ℹ Unsafe fix: If this is intentional, prepend removeTask with an underscore.
  
    35 35 │   }
    36 36 │   
    37    │ - function·removeTask(id:·string)·{
       37 │ + function·_removeTask(id:·string)·{
    38 38 │     const index = tasks.findIndex((task) => task.id === id);
    39 39 │     tasks.splice(index, 1);
  

app.svelte:7:1 lint/style/useConst  FIXABLE  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ✖ This let declares a variable that is only assigned once.
  
    6 │ const message = 'Tasks App';
  > 7 │ let currentFilter = $state<Filter>('all');
      │ ^^^
    8 │ let tasks = $state<Task[]>([]);
    9 │ let totalDone = $derived(tasks.reduce((total, task) => total + Number(task.done), 0));
  
  ℹ 'currentFilter' is never reassigned.
  
    4 │ import type { Filter, Task } from './types';
    5 │ 
  > 6 │ const message = 'Tasks App';
      │               ^^^^^^^^^^^^^
    7 │ let currentFilter = $state<Filter>('all');
    8 │ let tasks = $state<Task[]>([]);
  
  ℹ Safe fix: Use const instead.
  
     4  4 │   
     5  5 │   const message = 'Tasks App';
     6    │ - let·currentFilter·=·$state<Filter>('all');
        6 │ + const·currentFilter·=·$state<Filter>('all');
     7  7 │   let tasks = $state<Task[]>([]);
     8  8 │   let totalDone = $derived(tasks.reduce((total, task) => total + Number(task.done), 0));
  

app.svelte:8:1 lint/style/useConst  FIXABLE  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ✖ This let declares a variable that is only assigned once.
  
     6 │ const message = 'Tasks App';
     7 │ let currentFilter = $state<Filter>('all');
   > 8 │ let tasks = $state<Task[]>([]);
       │ ^^^
     9 │ let totalDone = $derived(tasks.reduce((total, task) => total + Number(task.done), 0));
    10 │ 
  
  ℹ 'tasks' is never reassigned.
  
    6 │ const message = 'Tasks App';
  > 7 │ let currentFilter = $state<Filter>('all');
      │                             ^^^^^
    8 │ let tasks = $state<Task[]>([]);
    9 │ let totalDone = $derived(tasks.reduce((total, task) => total + Number(task.done), 0));
  
  ℹ Safe fix: Use const instead.
  
     5  5 │   const message = 'Tasks App';
     6  6 │   let currentFilter = $state<Filter>('all');
     7    │ - let·tasks·=·$state<Task[]>([]);
        7 │ + const·tasks·=·$state<Task[]>([]);
     8  8 │   let totalDone = $derived(tasks.reduce((total, task) => total + Number(task.done), 0));
     9  9 │   
  

app.svelte:9:1 lint/style/useConst  FIXABLE  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ✖ This let declares a variable that is only assigned once.
  
     7 │ let currentFilter = $state<Filter>('all');
     8 │ let tasks = $state<Task[]>([]);
   > 9 │ let totalDone = $derived(tasks.reduce((total, task) => total + Number(task.done), 0));
       │ ^^^
    10 │ 
    11 │ const filteredTasks = $derived.by(() => {
  
  ℹ 'totalDone' is never reassigned.
  
     6 │ const message = 'Tasks App';
     7 │ let currentFilter = $state<Filter>('all');
   > 8 │ let tasks = $state<Task[]>([]);
       │                  ^^^^^^^^^
     9 │ let totalDone = $derived(tasks.reduce((total, task) => total + Number(task.done), 0));
    10 │ 
  
  ℹ Safe fix: Use const instead.
  
     6  6 │   let currentFilter = $state<Filter>('all');
     7  7 │   let tasks = $state<Task[]>([]);
     8    │ - let·totalDone·=·$derived(tasks.reduce((total,·task)·=>·total·+·Number(task.done),·0));
        8 │ + const·totalDone·=·$derived(tasks.reduce((total,·task)·=>·total·+·Number(task.done),·0));
     9  9 │   
    10 10 │   const filteredTasks = $derived.by(() => {
  

Checked 1 file in 5ms. No fixes applied.
Found 3 errors.
Found 8 warnings.
lint ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  ✖ Some errors were emitted while running checks.
  ```
</details>

<details>
<summary>VSCode Errors</summary>

```
[{
	"resource": ".../src/app.svelte",
	"owner": "_generated_diagnostic_collection_name_#3",
	"code": {
		"value": "lint/style/useConst",
		"target": {
			"$mid": 1,
			"path": "/linter/rules/use-const",
			"scheme": "https",
			"authority": "biomejs.dev"
		}
	},
	"severity": 8,
	"message": "This let declares a variable that is only assigned once.",
	"source": "biome",
	"startLineNumber": 6,
	"startColumn": 11,
	"endLineNumber": 6,
	"endColumn": 14,
	"relatedInformation": [
		{
			"startLineNumber": 6,
			"startColumn": 15,
			"endLineNumber": 6,
			"endColumn": 28,
			"message": "'currentFilter' is never reassigned. ",
			"resource": ".../src/app.svelte"
		}
	],
	"origin": "extHost2"
},{
	"resource": ".../src/app.svelte",
	"owner": "_generated_diagnostic_collection_name_#3",
	"code": {
		"value": "lint/style/useConst",
		"target": {
			"$mid": 1,
			"path": "/linter/rules/use-const",
			"scheme": "https",
			"authority": "biomejs.dev"
		}
	},
	"severity": 8,
	"message": "This let declares a variable that is only assigned once.",
	"source": "biome",
	"startLineNumber": 7,
	"startColumn": 25,
	"endLineNumber": 7,
	"endColumn": 28,
	"relatedInformation": [
		{
			"startLineNumber": 7,
			"startColumn": 29,
			"endLineNumber": 7,
			"endColumn": 34,
			"message": "'tasks' is never reassigned. ",
			"resource": ".../src/app.svelte"
		}
	],
	"origin": "extHost2"
},{
	"resource": ".../src/app.svelte",
	"owner": "_generated_diagnostic_collection_name_#3",
	"code": {
		"value": "lint/style/useConst",
		"target": {
			"$mid": 1,
			"path": "/linter/rules/use-const",
			"scheme": "https",
			"authority": "biomejs.dev"
		}
	},
	"severity": 8,
	"message": "This let declares a variable that is only assigned once.",
	"source": "biome",
	"startLineNumber": 8,
	"startColumn": 14,
	"endLineNumber": 8,
	"endColumn": 17,
	"relatedInformation": [
		{
			"startLineNumber": 8,
			"startColumn": 18,
			"endLineNumber": 8,
			"endColumn": 27,
			"message": "'totalDone' is never reassigned. ",
			"resource": ".../src/app.svelte"
		}
	],
	"origin": "extHost2"
},{
	"resource": ".../src/app.svelte",
	"owner": "_generated_diagnostic_collection_name_#3",
	"code": {
		"value": "lint/correctness/noUnusedImports",
		"target": {
			"$mid": 1,
			"path": "/linter/rules/no-unused-imports",
			"scheme": "https",
			"authority": "biomejs.dev"
		}
	},
	"severity": 4,
	"message": "This import is unused.",
	"source": "biome",
	"startLineNumber": 1,
	"startColumn": 8,
	"endLineNumber": 1,
	"endColumn": 17,
	"origin": "extHost2"
},{
	"resource": ".../src/app.svelte",
	"owner": "_generated_diagnostic_collection_name_#3",
	"code": {
		"value": "lint/correctness/noUnusedImports",
		"target": {
			"$mid": 1,
			"path": "/linter/rules/no-unused-imports",
			"scheme": "https",
			"authority": "biomejs.dev"
		}
	},
	"severity": 4,
	"message": "This import is unused.",
	"source": "biome",
	"startLineNumber": 2,
	"startColumn": 45,
	"endLineNumber": 2,
	"endColumn": 54,
	"origin": "extHost2"
},{
	"resource": ".../src/app.svelte",
	"owner": "_generated_diagnostic_collection_name_#3",
	"code": {
		"value": "lint/correctness/noUnusedVariables",
		"target": {
			"$mid": 1,
			"path": "/linter/rules/no-unused-variables",
			"scheme": "https",
			"authority": "biomejs.dev"
		}
	},
	"severity": 4,
	"message": "This variable message is unused.",
	"source": "biome",
	"startLineNumber": 4,
	"startColumn": 34,
	"endLineNumber": 4,
	"endColumn": 41,
	"origin": "extHost2"
},{
	"resource": ".../src/app.svelte",
	"owner": "_generated_diagnostic_collection_name_#3",
	"code": {
		"value": "lint/correctness/noUnusedVariables",
		"target": {
			"$mid": 1,
			"path": "/linter/rules/no-unused-variables",
			"scheme": "https",
			"authority": "biomejs.dev"
		}
	},
	"severity": 4,
	"message": "This variable totalDone is unused.",
	"source": "biome",
	"startLineNumber": 8,
	"startColumn": 18,
	"endLineNumber": 8,
	"endColumn": 27,
	"origin": "extHost2"
},{
	"resource": ".../src/app.svelte",
	"owner": "_generated_diagnostic_collection_name_#3",
	"code": {
		"value": "lint/correctness/noUnusedVariables",
		"target": {
			"$mid": 1,
			"path": "/linter/rules/no-unused-variables",
			"scheme": "https",
			"authority": "biomejs.dev"
		}
	},
	"severity": 4,
	"message": "This variable filteredTasks is unused.",
	"source": "biome",
	"startLineNumber": 9,
	"startColumn": 76,
	"endLineNumber": 11,
	"endColumn": 1,
	"origin": "extHost2"
},{
	"resource": ".../src/app.svelte",
	"owner": "_generated_diagnostic_collection_name_#3",
	"code": {
		"value": "lint/correctness/noUnusedVariables",
		"target": {
			"$mid": 1,
			"path": "/linter/rules/no-unused-variables",
			"scheme": "https",
			"authority": "biomejs.dev"
		}
	},
	"severity": 4,
	"message": "This function addTask is unused.",
	"source": "biome",
	"startLineNumber": 23,
	"startColumn": 12,
	"endLineNumber": 24,
	"endColumn": 3,
	"origin": "extHost2"
},{
	"resource": ".../src/app.svelte",
	"owner": "_generated_diagnostic_collection_name_#3",
	"code": {
		"value": "lint/correctness/noUnusedVariables",
		"target": {
			"$mid": 1,
			"path": "/linter/rules/no-unused-variables",
			"scheme": "https",
			"authority": "biomejs.dev"
		}
	},
	"severity": 4,
	"message": "This function toggleDone is unused.",
	"source": "biome",
	"startLineNumber": 30,
	"startColumn": 17,
	"endLineNumber": 34,
	"endColumn": 1,
	"origin": "extHost2"
},{
	"resource": ".../src/app.svelte",
	"owner": "_generated_diagnostic_collection_name_#3",
	"code": {
		"value": "lint/correctness/noUnusedVariables",
		"target": {
			"$mid": 1,
			"path": "/linter/rules/no-unused-variables",
			"scheme": "https",
			"authority": "biomejs.dev"
		}
	},
	"severity": 4,
	"message": "This function removeTask is unused.",
	"source": "biome",
	"startLineNumber": 35,
	"startColumn": 20,
	"endLineNumber": 38,
	"endColumn": 1,
	"origin": "extHost2"
}]
```
</details>

