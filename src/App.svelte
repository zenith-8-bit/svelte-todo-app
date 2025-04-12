<script>
  import { onMount } from 'svelte';
  
  let newItem = '';
  let editingIndex = null;
  let editValue = '';
  
  let todoList = [
      {text: 'Write my first post', status: true},
      {text: 'Upload the post to the blog', status: false},
      {text: 'Publish the post at Facebook', status: false}
  ];
  
  // Load from localStorage if available
  onMount(() => {
      const saved = localStorage.getItem('todo-list');
      if (saved) {
          todoList = JSON.parse(saved);
      }
  });
  
  function saveList() {
      localStorage.setItem('todo-list', JSON.stringify(todoList));
  }
  
  function addToList() {
      if (newItem.trim()) {
          todoList = [...todoList, {text: newItem.trim(), status: false}];
          newItem = '';
          saveList();
      }
  }

  function removeFromList(index) {
      todoList.splice(index, 1);
      todoList = todoList;
      saveList();
  }
  
  function startEditing(index) {
      editingIndex = index;
      editValue = todoList[index].text;
  }
  
  function saveEdit() {
      if (editingIndex !== null && editValue.trim()) {
          todoList[editingIndex].text = editValue.trim();
          todoList = todoList;
          editingIndex = null;
          saveList();
      }
  }
  
  function cancelEdit() {
      editingIndex = null;
  }
  
  function handleKeyDown(e) {
      if (e.key === 'Enter') {
          if (editingIndex !== null) {
              saveEdit();
          } else {
              addToList();
          }
      } else if (e.key === 'Escape' && editingIndex !== null) {
          cancelEdit();
      }
  }
</script>

<div class="app-container">
  <div class="todo-card">
      <div class="header">
          <h1 class="todo-title">T O · D O · L I S T</h1>
          <div class="title-underline"></div>
      </div>
      
      <div class="input-group">
          <input 
              bind:value={newItem} 
              type="text" 
              placeholder="What needs to be done?"
              on:keydown={handleKeyDown}
              class="todo-input"
          >
          <button on:click={addToList} class="add-button">
              <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="5.5" stroke-linecap="round" stroke-linejoin="round">
                  <line x1="12" y1="5" x2="12" y2="19"></line>
                  <line x1="5" y1="12" x2="19" y2="12"></line>
              </svg>
          </button>
      </div>
      
      <ul class="todo-items">
          {#each todoList as item, index}
              <li class:completed={item.status}>
                  <div class="item-content">
                      <input 
                          bind:checked={item.status} 
                          type="checkbox" 
                          class="checkbox"
                          id={`checkbox-${index}`}
                      >
                      <label for={`checkbox-${index}`} class="custom-checkbox">
                          <svg viewBox="0 0 12 10" class="checkmark">
                              <polyline points="1.5 6 4.5 9 10.5 1"></polyline>
                          </svg>
                      </label>
                      
                      {#if editingIndex === index}
                          <div class="edit-wrapper">
                              <input
                                  bind:value={editValue}
                                  on:keydown={handleKeyDown}
                                  on:blur={saveEdit}
                                  class="edit-input"
                                  autofocus
                              >
                          </div>
                      {:else}
                          <span 
                              on:click={() => startEditing(index)}
                              class="item-text"
                          >
                              {item.text}
                          </span>
                      {/if}
                      
                      <button on:click={() => removeFromList(index)} class="delete-button">
                          <svg  class = 'small-icon 'xmlns="http://www.w3.org/2000/svg"  viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.2" stroke-linecap="round" stroke-linejoin="round">
                              <path d="M3 6h18"></path>
                              <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6"></path>
                              <path d="M8 6V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
                              <line x1="10" y1="11" x2="10" y2="17"></line>
                              <line x1="14" y1="11" x2="14" y2="17"></line>
                          </svg>
                      </button>
                  </div>
              </li>
          {/each}
      </ul>
      
      {#if todoList.length === 0}
          <div class="empty-state">
              <svg xmlns="http://www.w3.org/2000/svg" width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.2" stroke-linecap="round" stroke-linejoin="round">
                  <path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"></path>
                  <line x1="12" y1="9" x2="12" y2="13"></line>
                  <line x1="12" y1="17" x2="12.01" y2="17"></line>
              </svg>
              <p>Your todo list is empty</p>
              <p class="hint">Add your first task above</p>
          </div>
      {/if}
  </div>
</div>

<style>
  /* Import fonts with smaller subsets to reduce loading size */
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap&text=ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789.,-%20');
  @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500&display=swap&text=TODLIST');
  
  /* Reset and base styles */
  :global(body) {
      margin: 0;
      padding: 0;
      min-height: 100vh;
      background-color: #0a0a0a;
      font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
      color: #f0f0f0;
      display: flex;
      justify-content: center;
      align-items: center;
      -webkit-font-smoothing: antialiased;
  }
  
  /* Main container - more compact with tighter padding */
  .app-container {
      width: 100%;
      max-width: 480px; /* Reduced from 580px */
      padding: 1.5rem 1rem; /* Reduced from 2rem */
      display: flex;
      align-items: center;
      justify-content: center;
      height: 100%;
  }
  
  /* Todo card with Notion-inspired clean aesthetic */
  .todo-card {
      width: 100%;
      background-color: transparent;
      padding: 14px;
  }
  
  /* Header section - reduced margin for more compact look */
  .header {
      margin-bottom: 1.75rem; /* Reduced from 2.5rem */
      text-align: center;
  }
  
  /* Title styling inspired by Notion's minimalist headings */
  .todo-title {
      margin: 0;
      font-size: 1.5rem; /* Reduced from 1.8rem */
      font-weight: 500;
      color: #f5f5f5;
      font-family: 'Space Grotesk', sans-serif;
      letter-spacing: 4px;
      position: relative;
      display: inline-block;
      padding-bottom: 6px; /* Reduced from 8px */
  }
  
  /* Subtle title underline like Notion's section dividers */
  .title-underline {
      height: 1px; /* Reduced from 2px */
      width: 50%; /* Reduced from 60% */
      background: linear-gradient(90deg, transparent 0%, #444 50%, transparent 100%);
      margin: 0 auto;
      opacity: 0.5;
  }
  
  /* Input group with tighter spacing */
  .input-group {
      display: flex;
      margin-bottom: 1.5rem; /* Reduced from 2rem */
      gap: 0.5rem; /* Reduced from 0.75rem */
  }
  
  /* Clean, minimal input field styled like Notion's inputs */
  .todo-input {
      flex: 1;
      padding: 0.75rem 1rem; /* Reduced from 0.9rem 1.25rem */
      border: 1px solid #2a2a2a;
      border-radius: 4px; /* Reduced from 6px */
      font-size: 0.9rem; /* Reduced from 1rem */
      transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
      background-color: #111;
      color: #f0f0f0;
      font-family: 'Inter', sans-serif;
      box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1); /* Reduced shadow */
  }
  
  /* Subtle focus state for input */
  .todo-input:focus {
      outline: none;
      border-color: #3a3a3a;
      background-color: #151515;
      box-shadow: 0 0 0 1px rgba(80, 80, 80, 0.2); /* Reduced from 2px */
  }
  
  .todo-input::placeholder {
      color: #555;
      font-weight: 400;
  }
  
  /* Button styled with Notion's minimal aesthetic */
  .add-button {
      background: #222;
      color: #f0f0f0;
      border: none;
      border-radius: 4px; /* Reduced from 6px */
      padding: 0 1rem; /* Reduced from 1.25rem */
      cursor: pointer;
      transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
      display: flex;
      align-items: center;
      justify-content: center;
      box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1); /* Reduced shadow */
  }
  
  .add-button:hover {
      background: #2a2a2a;
      transform: translateY(-1px);
  }
  
  .add-button:active {
      transform: translateY(0);
  }
  
  .add-button svg {
      stroke: #f0f0f0;
  }
  
  /* Todo items list with consistent spacing */
  .todo-items {
      list-style: none;
      padding: 0;
      margin: 0;
      max-width: 100%;
  }
  
  /* List items with consistent height and spacing */
  .todo-items li {
      padding: 0.75rem 0; /* Reduced from 1rem */
      border-bottom: 1px solid #222;
      transition: all 0.2s ease;
      max-width: 100%;
      word-break: break-word;
      height: 24px; /* Fixed height for consistency */
      display: flex;
      align-items: center;
  }
  
  .todo-items li:last-child {
      border-bottom: none;
  }
  
  /* Item content container with improved alignment */
  .item-content {
      display: flex;
      align-items: center; /* Changed from flex-start */
      gap: 0.75rem; /* Reduced from 0.85rem */
      padding: 0 0.25rem;
      max-width: 100%;
      width: 100%;
      height: 100%;
      margin:10px;
  }
  
  /* Checkbox styling with Notion-inspired appearance */
  .checkbox {
      opacity: 0;
      position: absolute;
  }
  
  /* Custom checkbox that resembles Notion's minimal checkboxes */
  .custom-checkbox {
      display: inline-flex;
      width: 16px; /* Reduced from 20px */
      height: 16px; /* Reduced from 20px */
      border: 1.5px solid #3a3a3a; /* Thinner border */
      border-radius: 3px; /* Reduced from 5px */
      cursor: pointer;
      flex-shrink: 0;
      transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
      position: relative;
      background-color: #1a1a1a;
      align-items: center;
      justify-content: center;
  }
  
  .custom-checkbox:hover {
      border-color: #4a4a4a;
  }
  
  /* Checkmark styling with Notion's clean aesthetic */
  .checkmark {
      width: 10px; /* Reduced from 12px */
      height: 8px; /* Reduced from 10px */
      stroke: transparent;
      stroke-width: 2.5; /* Reduced from 3 */
      stroke-linecap: round;
      stroke-linejoin: round;
      fill: none;
      transition: all 0.2s ease;
  }
  
  /* Checked state similar to Notion's understated style */
  .checkbox:checked + .custom-checkbox {
      background: #3a3a3a;
      border-color: #3a3a3a;
  }
  
  .checkbox:checked + .custom-checkbox .checkmark {
      stroke: #f0f0f0;
  }
      
  /* Text styling with Notion's clean typography */
  .item-text {
      flex: 1;
      cursor: text;
      padding: 0.1rem 0; /* Reduced from 0.2rem */
      transition: all 0.2s;
      background-color: transparent;
      font-family: 'Inter', sans-serif;
      font-size: 0.95rem; /* Reduced from 1.05rem */
      font-weight: 400;
      color: #e0e0e0;
      line-height: 1.5;
      margin-right: 0.5rem;
      max-width: calc(100% - 70px); /* Reduced from 80px */
      min-height: 22px; /* Reduced from 24px */
      display: inline-block;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
  }
  
  /* Edit mode wrapper with improved sizing */
  .edit-wrapper {
      flex: 1;
      margin-right: 0.5rem;
      max-width: calc(100% - 70px); /* Reduced from 80px */
  }
  
  /* Edit input with Notion-like minimal styling */
  .edit-input {
      width: 100%; /* Changed from fixed 40px */
      padding: 0.1rem 0; /* Reduced from 0.2rem */
      font-size: 0.95rem; /* Reduced from 1.05rem */
      font-family: 'Inter', sans-serif;
      background-color: transparent;
      color: #f0f0f0;
      border: none;
      border-radius: 0;
      outline: none;
      box-shadow: none;
      resize: none;
      line-height: 1.5;
      min-height: 22px; /* Reduced from 24px */
  }
  
  .edit-input:focus {
      outline: none;
      box-shadow: none;
      border: none;
  }
  
  /* Delete button with subtle Notion-inspired hover state */
  .delete-button {
      background-color: transparent;
      border: none;
      color: #a9a0ab;
      cursor: pointer;
      transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
      padding: 0.35rem; /* Reduced from 0.5rem */
      border-radius: 3px; /* Reduced from 5px */
      display: flex;
      align-items: center; /* Changed from flex-start */
      justify-content: center;
      opacity: 0.8; /* Hidden by default, visible on hover */
      margin-left: auto;
     
  }
  
  /* Show delete button on list item hover - Notion-like behavior */
  .item-content:hover .delete-button {
      opacity: 1;
      color:#9c2b2b;
      background-color: transparent;


  }
  
  .delete-button:hover {
      opacity: 3;
      color: #9c2b2b;
      background-color: transparent;
  }
  
  /* Completed items with subtle styling like Notion */
  .completed .item-text {
      color: #9c2b2b;
      text-decoration: line-through;
      opacity: 0.8;
  }
  
  /* Empty state with Notion's minimalist aesthetic */
  .empty-state {
      text-align: center;
      padding: 2rem 0; /* Reduced from 3rem */
      color: #555;
      font-family: 'Inter', sans-serif;
  }
  
  .empty-state svg {
      stroke: #555;
      margin-bottom: 1rem; /* Reduced from 1.5rem */
      opacity: 0.8;
  }
  
  .empty-state p {
      margin: 0.25rem 0;
      font-size: 1rem; /* Reduced from 1.1rem */
  }
  
  .empty-state .hint {
      font-size: 0.85rem; /* Reduced from 0.95rem */
      color: #444;
      margin-top: 0.5rem;
  }
  .small-icon {
    width: 16px;
    height: 16px;
}
</style>