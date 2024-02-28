<template lang="">
    <div v-if="users.length !== 0">
        <div class=card v-for="user in users" :key="user.id">
          <h4>{{user.firstName}}</h4>
					<button class="danger btn" @click="$emit('remove', user.id)">Удалить</button>
        </div>
    </div>
		<div class="card inline" v-else>
			<h4>Список пользователей пока пуст</h4>
			<button class="btn" @click="$emit('load')" :disabled="users.length === 0" >Загрузить список</button>
		</div>

</template>

<script>
export default {
	props: ['users'],
	emits: ['load', 'remove']
}
</script>

<style scoped lang="scss">
  .card{
		display: flex;
		justify-content: space-between;
		align-items: center;
	}
</style>