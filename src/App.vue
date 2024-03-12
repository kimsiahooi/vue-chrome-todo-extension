<template>
  <div class="w-[350px] bg-[#f4f4f4]">
    <div id="container" class="bg-white p-[15px] rounded-lg shadow-[0_4px_8px_rgba(0,0,0,0.1)]">
      <input
        type="text"
        id="new-task"
        placeholder="Add a new task..."
        class="p-[10px] w-full mb-[10px] border border-solid border-[#ddd] rounded outline-none"
        @keydown.enter="addTaskFromInput"
        ref="taskRef" />
      <button
        id="add-task"
        class="p-[10px] w-full bg-[#4caf50] text-white border-none rounded cursor-pointer mt-[10px] text-[13px] transition-opacity duration-300 ease-in-out hover:opacity-80"
        @click="addTaskFromInput">
        Add Task
      </button>
      <ul id="task-list" class="mt-5 p-0">
        <li
          v-for="task in tasks"
          :key="task.id"
          class="flex items-center p-[10px] bg-[#e0e0e0] mb-[5px] rounded overflow-hidden">
          <input type="checkbox" class="cursor-pointer" :checked="task.isComplete" @click="() => toggleTask(task.id)" />
          <span class="flex-1 ml-[10px] break-all" :class="task.isComplete ? 'line-through' : ''">{{ task.name }}</span>
          <button
            class="ml-[5px] py-[5px] px-2 border-none bg-[#F2EEE9] rounded cursor-pointer text-xs hover:opacity-80"
            @click="() => editTask(task.id, task.name)">
            Edit
          </button>
          <button
            class="ml-[5px] py-[5px] px-2 border-none bg-[#F2EEE9] rounded cursor-pointer text-xs hover:opacity-80"
            @click="() => deleteTask(task.id)">
            Delete
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { nanoid } from 'nanoid';

export default {
  data() {
    return {
      tasks: [],
    };
  },
  methods: {
    addTaskFromInput() {
      const taskValue = this.$refs.taskRef.value;
      if (taskValue) {
        const temp = [...this.tasks, { id: nanoid(), name: taskValue, isComplete: false }];
        localStorage.setItem('oks-todo-extension', JSON.stringify(temp));
        this.tasks = temp;
        this.$refs.taskRef.value = '';
      }
    },
    toggleTask(taskId) {
      const tempTasks = this.tasks.map((task) => {
        if (task.id === taskId) {
          task.isComplete = !task.isComplete;
        }
        return task;
      });

      localStorage.setItem('oks-todo-extension', JSON.stringify(tempTasks));

      this.tasks = tempTasks;
    },
    deleteTask(taskId) {
      const tempTasks = this.tasks.filter((task) => task.id !== taskId);

      localStorage.setItem('oks-todo-extension', JSON.stringify(tempTasks));
      this.tasks = tempTasks;
    },
    editTask(taskId, taskName) {
      const newTask = prompt('Edit Your Task:', taskName);

      if (newTask !== null) {
        const tempTasks = this.tasks.map((task) => {
          if (task.id === taskId) {
            task.name = newTask;
          }
          return task;
        });

        localStorage.setItem('oks-todo-extension', JSON.stringify(tempTasks));
        this.tasks = tempTasks;
      }
    },
  },
  mounted() {
    if (!localStorage.getItem('oks-todo-extension')) {
      localStorage.setItem('oks-todo-extension', JSON.stringify(this.tasks));
    } else {
      this.tasks = JSON.parse(localStorage.getItem('oks-todo-extension'));
    }
  },
};
</script>
