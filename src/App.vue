<template>
	<div class="container">
		<div class="config">
			<div class="info">
				<!-- <span>Nombre total de paires : 9</span>  -->
				<span>Nombre de tentatives : {{ nbTentative }} </span> | <span>Temps de jeu : {{ timestamp }} </span> |
				<span>Nombre de paires trouvées : {{ pairesTrouvées }} </span>
			</div>
			<button disabled class="btn btn-danger" id="recommencer" @click.self="recommencer">Recommencer</button>
		</div>
		<div class="congrats">
			<div class="fireworks" style="display: none">
				<h2 class="top">Bravo !</h2>
			</div>
		</div>
		<div class="cards">
			<div class="card" v-for="(el, i) in pairesImages" :key="i" :data-id="el.id" @click.self="rotate">
				<div class="card__front">
					<div class="card-body">
						<img :src="el.img" alt="un animal" class="rounded" />
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import { Fireworks } from "fireworks-js";
export default {
	name: "App",
	data() {
		return {
			images: [
				require("./assets/imgCard/elephant-min.jpeg"),
				require("./assets/imgCard/gepard-min.jpeg"),
				require("./assets/imgCard/lapin-min.jpeg"),
				require("./assets/imgCard/loup-min.jpeg"),
				require("./assets/imgCard/renard-min.jpeg"),
				require("./assets/imgCard/rhino-min.jpeg"),
				require("./assets/imgCard/singe-min.jpeg"),
				require("./assets/imgCard/tiger-min.jpeg"),
				require("./assets/imgCard/tortue-min.jpeg"),
			],
			pairesImages: [],
			nbTentative: 0,
			paires: [],
			pairesTrouvées: 0,
			timestamp: 0,
			timer: null,
			startTimer: false,
		};
	},
	watch: {
		startTimer() {
			console.log("Je commence");
			if (this.startTimer) {
				let minutes = 0;
				let secondes = 0;
				this.timer = setInterval(() => {
					secondes++;
					if (secondes < 10) {
						secondes = "0" + secondes;
					}
					if (secondes >= 60) {
						secondes = 0;
						minutes++;
					}
					this.timestamp = minutes + " min " + secondes + " sec";
				}, 1000);
			}
		},
	},
	methods: {
		// le melange des cartes
		melange() {
			let createPairs = [];
			for (let i = 0; i < this.images.length; i++) {
				createPairs.push({ id: i, img: this.images[i] }, { id: i, img: this.images[i] });
			}
			this.pairesImages = createPairs.sort(() => 0.5 - Math.random());
		},
		// la rotation des cartes
		rotate(e) {
			this.startTimer = true;
			document.querySelector("#recommencer").removeAttribute("disabled");
			// retourner la carte
			//  retourner une autre carte
			// verifier si elles sont paires
			// si paires  ( inc nb paire trouver + tentative + timerstart)
			// sinon retourner les deux cartes ( inc tentative + timerstart)
			if (this.paires.length < 2) {
				e.target.classList.toggle("rotate");
				this.paires.push(e.target);
				console.log("je retourne la carte");
				if (this.paires.length === 2) {
					this.nbTentative++;
					if (this.paires[0].dataset.id === this.paires[1].dataset.id) {
						this.pairesTrouvées++;
						this.paires = [];
					} else {
						setTimeout(() => {
							this.paires[0].classList.remove("rotate");
							this.paires[1].classList.remove("rotate");
							this.paires = [];
						}, 1000);
					}
				}
			}
			if (this.pairesTrouvées === 9) {
				clearInterval(this.timer);
				document.querySelector("#recommencer").innerHTML = "Rejouer";
				this.startTimer = false;
			}

			// Celebration reussite

			if (this.pairesTrouvées === 9) {
				clearInterval(this.timer);
				document.querySelector("#recommencer").innerHTML = "Rejouer";
				this.startTimer = false;
				this.fireworks();
				setTimeout(function () {
					document.querySelector(".cards").style.display = "none";
				}, 2000);
			}
		},
		// restart
		recommencer(e) {
			this.nbTentative = 0;
			this.nbTrouvées = 0;
			this.timestamp = 0;
			this.pairesTrouvées = 0;
			this.startTimer = false;
			e.target.setAttribute("disabled", "");
			clearInterval(this.timer);
			document.querySelector(".fireworks").style.display = "none";
			this.reset();
			this.melange();
			document.querySelector("#recommencer").innerHTML = "Recommencer";
			document.querySelector(".cards").style.display = "flex";
			// stopFireworks();
		},
		// init
		reset() {
			Array.from(document.querySelectorAll(".card")).forEach((card) => {
				card.classList.remove("rotate");
			});
		},
		fireworks() {
			const container = document.querySelector(".fireworks");
			document.querySelector(".fireworks").style.display = "block";
			const fireworks = new Fireworks(container, {
				acceleration: 1.1,
				particles: 100,
				explosion: 15,
			});
			fireworks.start();
		},
		stopFireworks() {
			this.fireworks.stop();
		},
	},
	mounted() {
		this.melange();
		this.fireworks;
	},
	components: {},
};
</script>

<style lang="scss" src="./assets/app.scss"></style>
// perfectionner
