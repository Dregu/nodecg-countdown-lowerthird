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
	<div class="timer" v-bind:class="{ hidden: hidden }">
		<ul class="list">
			<li><h1 class="mins">{{mins}}</h1>
			<li><h1 class="separator">:</h1>
			<li><h1 class="secs">{{secs}}</h1>
		</ul>
		<hr class="line"/>
		<h1 class="text">{{text}}</h1>
	</div>
</template>

<script>
import {TimelineMax, CSSPlugin, Power2, Power4} from "gsap/TweenMax";
const timerRep = nodecg.Replicant('timer');

export default {
	data() {
		return {
			mins: null,
			secs: null,
			hidden: 'false',
			text: ''
		};
	},
	created() {
		NodeCG.waitForReplicants(timerRep).then(this.listen);
	},
	methods: {
		listen() {
			console.log("listening for timer changes");
			timerRep.on('change', newVal => {
				this.mins = this.pad(newVal.minutes);
				this.secs = this.pad(newVal.seconds);
				this.text = String(newVal.text).toUpperCase();
				this.hidden = newVal.hidden;
			});
		},
		pad(num) {
			return num < 10 ? `0${num}` : `${num}`;
		}
	}
};
</script>

<style lang="scss" scoped>
@import url("https://use.typekit.net/mah1qnl.css");
@import '../../../shared/style/main.scss';

.timer {
	transition: all 0.5s;
	position: absolute;
	left: $width / 2;
	width: 600px;
	height: 240px;
	margin-top: -120px;
	top: 520px;
	transform: translateX(-50%);
	opacity: 1;

	* {
		text-align: center;
		margin: 0;
		color: $primary;
	}

	.list {
		transition: all 1s;
		position: relative;
		left: 0;
		list-style: none;
		margin-bottom: 160px;
		li {
			h1 {
				display: inline;
				line-height: 1.25em;
				font-size: 140px;
				font-family: futura-pt, sans-serif;
				position: absolute;
			}

			.mins {
				right: 55%;
			}
			.separator {
				left: 50%;
				transform: translateX(-50%);
			}
			.secs {
				left: 55%;
			}
		}
	}
	.line {
		margin: 10px auto;
		border: none;
    height: 6px;
		background: radial-gradient(circle, rgba(255, 255, 255, 0.5), rgba(255, 255, 255, 0));
		width: 720px;
		margin-left: -60px;
	}
	.text {
		transition: all 1s;
		position: relative;
		left: 0;
		line-height: 1em;
		font-size: 67px;
	}
}

.timer.hidden {
	opacity: 0;

	.list {
		left: -1000px;
	}
	.text {
		left: 1000px;
	}
}

</style>
