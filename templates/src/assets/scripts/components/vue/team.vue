<script>
export default {
	methods: {
		async fetchData () {
			try {
				this.meta.isLoading = true;
				const params = new URLSearchParams({
					limit: this.meta.limit,
					page: this.meta.page,
				});

				if (this.meta.duty !== '') {
					params.set('duty', this.meta.duty);
				}

				const response = await fetch('https://challenge-api.view.agentur-loop.com/api/?' + params.toString(), {
					headers: {
						Authorization: 'Bearer 0123456789',
						Accept: 'application/json',
					},
				});
				const result = await response.json();
				this.meta.isLoading = false;
				return result.data;
			} catch (err) {
				console.log(err);
			}
		},
		async init () {
			this.meta.page = 1;
			const result = await this.fetchData();
			this.members = result.data;
			this.itemWidth = (100 / this.meta.limit) + '%';
		},
		async loadMore () {
			this.meta.page = this.meta.page + 1;
			const result = await this.fetchData();
			this.members.push(...result.data);
		},
		async updateGridSize (limit) {
			this.meta.limit = limit;
			this.init();
		},
		async filterByDuty (duty) {
			this.meta.duty = duty;
			this.init();
		},
	},
	async mounted () {
		this.init();
	},
	data () {
		return {
			count: 0,
			members: [],
			meta: {
				duty: '',
				limit: 5,
				page: 1,
				isFinished: false,
				isLoading: false,
			},
			itemWidth: '20%',
		};
	},
};
</script>

<template>
    <div class="team-main">
		<div class="team-header container">
			<div>
				<h2 class="h2 text-content__headline color-light">unser team</h2>
				<h5 class="h5 text-content__headline color-light">Subtitle goes here</h5>
			</div>
			<ul class="team-filter">
				<li>
					<a href="#" @click.prevent="filterByDuty('')"
					class="team-filter__item"
					:class="{'active': meta.duty == ''}">Show all</a>
				</li>
				<li>
					<a href="#" @click.prevent="filterByDuty('trim')"
					class="team-filter__item"
					:class="{'active': meta.duty == 'trim'}">Trim</a>
				</li>
				<li>
					<a href="#" @click.prevent="filterByDuty('tactic')"
					class="team-filter__item"
					:class="{'active': meta.duty == 'tactic'}">Tactic</a>
				</li>
				<li>
					<a href="#" @click.prevent="filterByDuty('helmsman')"
					class="team-filter__item"
					:class="{'active': meta.duty == 'helmsman'}">Helmsman</a>
				</li>
			</ul>
		</div>
        <div class="team-list">
            <div class="team-list__item" :style="{'width': itemWidth}" v-for="member in members" :key="member.id">
                <div class="team-list__item__info">
                    <h5 class="h5 color-primary">{{ member.name }}</h5>
                    <p class="text">{{ member.duties }}</p>
                </div>
                <picture class="team-list__item__img">
                    <img :src="member.image" :alt="member.name">
                </picture>
            </div>
		</div>
		<div class="team-pagination">
            <a href="#" @click.prevent="updateGridSize(4)" class="team-pagination__item" :class="{'active': meta.limit == '4'}">4</a>
            <a href="#" @click.prevent="updateGridSize(5)" class="team-pagination__item" :class="{'active': meta.limit == '5'}">5</a>
            <a href="#" @click.prevent="updateGridSize(6)" class="team-pagination__item" :class="{'active': meta.limit == '6'}">6</a>
		</div>
        <button @click="loadMore" class="load-more color-light">Load more</button>
    </div>
</template>
