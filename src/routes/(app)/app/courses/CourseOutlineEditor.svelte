<script lang="ts">
  import type { CourseOutline, CourseTopic } from '$lib/types';
  import { fade, slide } from 'svelte/transition';
  import { preventDefault } from 'svelte/legacy';
 import Icon from '$lib/icons/Icon.svelte'
  interface  Props{
    outline: CourseOutline;
    onSave: (outline: CourseOutline) => void;
  }
  
  let { outline, onSave }: Props = $props();
  
  let editingTopic = $state<string | null>(null);
  let draggedTopic = $state<string | null>(null);
   
  
  function addTopic() {
    outline.topics = [
      ...outline.topics,
      {
        id: crypto.randomUUID(),
        title: '',
        estimatedHours: 1,
        subtopics: []
      }
    ];
    editingTopic = outline.topics[outline.topics.length - 1].id;
  }
  
  function addSubtopic(topicId: string) {
    const topic = outline.topics.find(t => t.id === topicId);
    if (topic) {
      topic.subtopics = [
        ...(topic.subtopics || []),
        {
          id: crypto.randomUUID(),
          title: '',
          estimatedHours: 1
        }
      ];
    }
  }
  
  function removeTopic(id: string) {
    outline.topics = outline.topics.filter(t => t.id !== id);
    if (editingTopic === id) editingTopic = null;
  }
  
  function removeSubtopic(topicId: string, subtopicId: string) {
    const topic = outline.topics.find(t => t.id === topicId);
    if (topic && topic.subtopics) {
      topic.subtopics = topic.subtopics.filter(st => st.id !== subtopicId);
    }
  }

  function handleDragStart(topicId: string) {
    draggedTopic = topicId;
  }

  function handleDragOver(topicId: string) {
    if (!draggedTopic || draggedTopic === topicId) return;
    
    const fromIndex = outline.topics.findIndex(t => t.id === draggedTopic);
    const toIndex = outline.topics.findIndex(t => t.id === topicId);
    
    const topics = [...outline.topics];
    const [moved] = topics.splice(fromIndex, 1);
    topics.splice(toIndex, 0, moved);
    
    outline.topics = topics;
  }
</script>

<div class="space-y-6">
  <div class="flex justify-between items-center">
    <h3 class="text-lg font-semibold text-gray-900">Course Outline</h3>
    <button 
      type="button"
      class="text-sm text-primary-600 hover:text-primary-700"
      onclick={addTopic}
    >
      Add Topic
    </button>
  </div>

  <div class="space-y-4">
    {#each outline.topics as topic (topic.id)}
      <div 
        class="border rounded-lg p-4 space-y-4 cursor-move"
        draggable="true"
        ondragstart={() => handleDragStart(topic.id)}
        ondragover={preventDefault(handleDragOver(topic.id))}
        in:slide|local
        out:slide|local
      >
        <div class="flex items-start justify-between">
          {#if editingTopic === topic.id}
            <div class="flex-1 space-y-2">
              <input
                type="text"
                bind:value={topic.title}
                class="block w-full rounded-md border-gray-300 shadow-sm focus:border-primary-500 focus:ring-primary-500"
                placeholder="Topic title"
              />
              <textarea
                bind:value={topic.description}
                rows="2"
                class="block w-full rounded-md border-gray-300 shadow-sm focus:border-primary-500 focus:ring-primary-500"
                placeholder="Topic description"
              ></textarea>
              <div class="flex items-center gap-2">
                <label class="text-sm text-gray-700">Estimated Hours:</label>
                <input
                  type="number"
                  bind:value={topic.estimatedHours}
                  min="1"
                  class="w-20 rounded-md border-gray-300 shadow-sm focus:border-primary-500 focus:ring-primary-500"
                />
              </div>
            </div>
          {:else}
            <div>
              <h4 class="font-medium text-gray-900">{topic.title}</h4>
              {#if topic.description}
                <p class="text-sm text-gray-600 mt-1">{topic.description}</p>
              {/if}
              <p class="text-sm text-gray-500 mt-1">
                Estimated: {topic.estimatedHours} hours
              </p>
            </div>
          {/if}
          
          <div class="flex items-center gap-2 ml-4">
            <button
              type="button"
              class="text-gray-600 hover:text-gray-900"
              onclick={() => editingTopic = editingTopic === topic.id ? null : topic.id}
            >
            
                  <Icon icon={editingTopic === topic.id ? 'check' : 'note'} h=5 w=5
                  />
            </button>
            <button
              type="button"
              class="text-red-600 hover:text-red-800"
              onclick={() => removeTopic(topic.id)}
            >
              <Icon icon="trash" h=5 w=5 />
            </button>
          </div>
        </div>

        <!-- Subtopics -->
        <div class="pl-6 space-y-2">
          {#if topic.subtopics}
            {#each topic.subtopics as subtopic (subtopic.id)}
              <div 
                class="flex items-center justify-between py-2"
                in:slide|local
              >
                <div class="flex items-center gap-4">
            {#if editingTopic === topic.id}
                  <input
                    type="text"
                    bind:value={subtopic.title}
                    class="rounded-md border-gray-300 shadow-sm flex focus:border-primary-500 focus:ring-primary-500"
                    placeholder="Subtopic title"
                  />
                  <input
                    type="number"
                    bind:value={subtopic.estimatedHours}
                    min="1"
                    class="w-20 rounded-md border-gray-300 shadow-sm flex focus:border-primary-500 focus:ring-primary-500"
                  />
                      {:else}
            <p
                    class="rounded-md border-gray-300 shadow-sm flex focus:border-primary-500 focus:ring-primary-500"
                    
                  >
            {subtopic.title}
                  </p>
                  <span
                    class="w-20 rounded-md border-gray-300 shadow-sm flex focus:border-primary-500 focus:ring-primary-500">
                     {subtopic.estimatedHours} hour(s)
                  </span>
            
            
            {/if}
              </div>
                <button
                  type="button"
                  class="text-red-600 hover:text-red-800"
                  onclick={() => removeSubtopic(topic.id, subtopic.id)}
                >
                 <Icon icon="close" h=4 w=4/> 
                </button>
                </div>
            {/each}
          {/if}
          
          <button
            type="button"
            class="text-sm text-primary-600 hover:text-primary-700"
            onclick={() => addSubtopic(topic.id)}
          >
            Add Subtopic
          </button>
        </div>
      </div>
    {/each}
  </div>
</div>
