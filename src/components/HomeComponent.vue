<template>
    <LayOut>
      <template #header>
        <HeaderComponent />
      </template>
      <template #resume>
        <ResumeComponent
          :label="label"
          :labelText="'Ahorro total'"
          :total-amount="totalAmount"
          :amount="amount"
        >
          <template #graphic>
            <GraphicComponent 
            :amounts="amounts"
            @select="select" />
          </template>
          <template #action>
            <ActionComponent @create="create" />
          </template>
        </ResumeComponent>
      </template>
      <template #movements>
        <MovementIndex
          :movements="movements"
          @remove="remove"
        />
      </template>
    </LayOut>
  </template>
  
  <script setup>
  import LayOut from '@/components/LayOut.vue';
  import HeaderComponent from '@/components/HeaderComponent.vue';
  import ResumeComponent from '@/components/Resume/IndexComponent.vue';
  import MovementIndex from '@/components/Movements/MovementIndex.vue';
  import ActionComponent from '@/components/ActionComponent.vue';
  import GraphicComponent from '@/components/Resume/GraphicComponent.vue';
  import { ref, computed, onMounted } from 'vue';
  
  let amount = ref(null);
  const label = ref(null);
  const movements = ref([]);
  
  const amounts = computed(() => {
    const lastDays = movements.value
      .filter(m => {
        const today = new Date();
        const oldDate = new Date(today);
        oldDate.setDate(today.getDate() - 30);
        return new Date(m.time) >= oldDate;
      })
      .map(m => m.amount);
  
    return lastDays.map((m, i) => {
      const lastMovements = lastDays.slice(0, i + 1);
      return lastMovements.reduce((sum, movement) => sum + movement, 0);
    });
  });
  
  const create = (movement) => {
    movements.value.push(movement);
    save();
  };
  
  const remove = (id) => {
    const index = movements.value.findIndex(m => m.id === id);
    if (index !== -1) {
      movements.value.splice(index, 1);
      save();
    }
  };
  
  const save = () => {
    localStorage.setItem('movements', JSON.stringify(movements.value));
  };
  
  onMounted(() => {
    const storedMovements = localStorage.getItem('movements');
    if (storedMovements) {
      movements.value = JSON.parse(storedMovements).map(m => ({
        ...m,
        time: new Date(m.time),
      }));
    }
  });
  
  const totalAmount = computed(() => {
    return movements.value.reduce((sum, m) => sum + m.amount, 0);
  });

  const select = (el) => {
    console.log(el);
    return amount = el;
  }
  </script>
  

  
