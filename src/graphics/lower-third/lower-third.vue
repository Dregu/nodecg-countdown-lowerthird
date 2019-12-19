<!-- Copyright (C) 2018 Daniel Shields

 This program is free software: you can redistribute it and/or modify
 it under the terms of the GNU Affero General Public License as
 published by the Free Software Foundation, either version 3 of the
 License, or (at your option) any later version.

 This program is distributed in the hope that it will be useful,
 but WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 GNU Affero General Public License for more details.

 You should have received a copy of the GNU Affero General Public License
 along with this program.  If not, see <http://www.gnu.org/licenses/>.
-->

<template>
	<div ref="lt" class="lower-third">
		<div ref="content" class="content">
			<template v-if="running == 'casters'">
				<casters :style="'opacity: ' + contentOpacity"/>
			</template>
			<template v-else-if="running === 'desk'">
				<desk :style="'opacity: ' + contentOpacity"/>
			</template>
			<template v-else-if="running === 'host'">
				<host :style="'opacity: ' + contentOpacity"/>
			</template>
			<template v-else-if="running === 'analysts'">
				<analysts :style="'opacity: ' + contentOpacity"/>
			</template>
		</div>
	</div>
</template>

<script>
import {TimelineMax, CSSPlugin, Power2, Power4} from "gsap/TweenMax";
import Casters from './types/Casters.vue';
import Desk from './types/Desk.vue';
import Predictions from './types/Predictions.vue';
import Host from './types/Host.vue';
import Analysts from './types/Analysts.vue';
import Generic from './types/Generic.vue';

export default {
	data() {
		return {
			tl: new TimelineMax({paused: true, onReverseComplete: this.finish, onReverseCompleteScope: this}),
			running: false,
			contentOpacity: 0
		};
	},
	created() {
		nodecg.listenFor('startLowerThird', type => {
			this.start(type);
		});
		nodecg.listenFor('endLowerThird', this.stop);
	},
	mounted() {
		this.tl.set(this, {contentOpacity: 0})
		this.tl.set(this.$refs.content, {opacity: 1}, '-=0.3');
		this.tl.set(this, {opacity: 1}, '-=0.3');
		//this.tl.to(this.$refs.content, 1.5, {width: '1580px', ease: Power2.easeOut});
		this.tl.to(this.$refs.content, 1, {bottom: '50px', ease: Power2.easeOut});
		this.tl.to(this, 0.5, {contentOpacity: 1}, '-=0.66')
		this.tl.to(this.$refs.lt, 0.5, {opacity: 1}, '-=0.66')

		// Skip transition if we should already be running
		// Useful if graphic needs to be reloaded etc.
		nodecg.readReplicant('lowerThirdCurrent', val => {
			if (val) {
				this.running = val;
				this.tl.seek('-=0');
				this.tl.play();
			}
		});
	},
	methods: {
		start(type) {
			this.running = type;
			this.tl.play();

			console.log(this.running);
		},
		stop() {
			this.tl.reverse();
		},
		finish() {
			this.tl.pause();
			this.running = false;
			nodecg.sendMessage('finishedLowerThird');
		}
	},
	components: {
		Casters,
		Desk,
		Predictions,
		Generic,
		Host,
		Analysts
	}
};
</script>

<style lang="scss" scoped>

@import url("https://use.typekit.net/mah1qnl.css");
@import '../../../shared/style/main.scss';

.lower-third {
	width: 100%;
	height: 40%;
	position: absolute;
	bottom: 100px;
	opacity: 0;
}

.content {
	position: absolute;
	bottom: -320px;
	left: 175px;
	height: 150px;
	width: 1580px;
	background-color: rgba(0, 0, 0, 0.6);
	border: 2px solid $primary;
	border-radius: 15px;
	z-index: -4;
	overflow: hidden;
	white-space: nowrap;
	opacity: 0;
}

</style>
