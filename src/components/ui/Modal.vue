<!--
// 
//  Modal Component
//  _________________________
// 
// 	Show modal that accepts props for title and footer buttons, slot as content
//
//		#Template
//			Modal that floats in the middle of the screen, on mobile it floats fixed bottom, full width, 
//			Top header bar with title and optional icon, middle scoll content, fixed footer with buttons, 
//			Lightbox behind modal - click or swipe to dismiss, 
//			 
//			Slots: 
//				Single slot for modal body
// 
//		#Props
// 			show: condition to show modal
// 			size: large (default/empty), small
// 			color: primary (default/empty), other button colors
// 			reverseFooter: reverse direction of footer
// 			dismissText: text for dismiss button
// 			confirmText: text for main action/confirm button
// 			confirmIcon: icon for confirm button
//
//		#Script
// 			mixins
//				defines escape key to close help modal
// 
-->

<template>
	<div>
		<!-- Lightbox -->
		<transition name="lightbox">
			<div class="lightbox" v-if="show"></div>
		</transition>
		<!-- Modal -->
		<transition name="modal">

			<div class="modal-wrapper" v-on:click.self="$emit('dismissed')" v-if="show" v-touch:swipe.self="() => $emit('dismissed')">

				<div class="modal" v-bind:class="size">
					<!-- Header -->
					<div class="modal-title" v-touch:swipe.down="() => $emit('dismissed')">
						<slot name="header"></slot>
					</div>
					<!-- Body Content -->
					<div class="modal-body no-scrollbars">
						<slot name="body"></slot>
					</div>
					<!-- Footer/Buttons -->
					<div class="modal-footer" v-bind:class="{'center': !dismissText || !confirmText, 'reverse': reverseFooter}" v-if="confirmText || dismissText">
						<!-- Dismiss -->
						<button class="button invert" @click="$emit('dismissed')" aria-label="Dismiss" v-if="dismissText">
							<i class="far fa-times"></i>
							<span>{{dismissText}}</span>
						</button>
						<!-- Leave -->
						<button class="button" v-bind:class="color" @click="$emit('confirmed')" v-bind:aria-label="confirmText" v-if="confirmText">
							<span>{{confirmText}}</span>
							<i v-bind:class="confirmIcon ? confirmIcon : 'fas fa-long-arrow-right'"></i>
						</button>
					</div>
				</div> <!-- End modal body/form -->

			</div>

		</transition>
	</div>
</template>

<script>
// Keyboard shortcuts mixin - esc closes modal
import shortcut from "@/components/mixins/keyboardShortcutsMixin.js";

export default {
	name: "Modal",
	mixins: [
		// Dismiss modal with escape
		shortcut('escape', function() {
			this.$emit('dismissed')
		}),
	],
	components: {
	},
	props: [
		'show',
		'size',
		'color',
		'reverseFooter',
		'dismissText',
		'confirmText',
		'confirmIcon',
	],
	data() {
		return {
		};
	},
	created() {

	},
	mounted(){
	},
	methods: {
	}	
}	

</script>

<style lang="less">
	@import '~@/styles/variables.less';

	// Universal Lightbox
	// Blank div with lightbox class
	.lightbox{
		height: 100%;
		width: 100vw;
		background-color: var(--lightbox);
		position: fixed;
		bottom: 0;
		left: 0;
		z-index: 500;
		backdrop-filter: blur(2px);
	}

	// Modals
	// Flex wrapper - click outside body to close
	.modal-wrapper{
		display: flex;
		flex-direction: column;
		justify-content: center;
		height: 100%;
		width: 100%;
		position: fixed;
		top: 0;
		left: 0;
		z-index: 1000;
		transform: scaleX(1);
		transition: var(--transition);
		transform-origin: bottom center;

		// Modal body
		.modal{
			display: flex;
			flex-direction: column;
			background-color: var(--pageBackground);
			margin: 0 auto;
			width: 90%;
			max-width: 500px;
			border-radius: var(--borderRadiusMd);
			position: relative;
			max-height: 75vh;
			border: var(--borderWidth) solid var(--border);


			// Absolute bottom, larger on mobile
			@media (max-width: @screenSM) {
				width: 100%;
				position: absolute;
				bottom: 0;
				left: -1px;
				max-width: 100%;
				border-top-left-radius: 30px;
				border-top-right-radius: 30px;
				// border:none;
			}

			// Close modal button, floats top right
			.modal-close{
				display: flex;
				flex-direction: column;
				justify-content: center;
				font-size: 18px;
				position: absolute;
				top: 0;
				right: 0;
				padding: 6px 10px;
				opacity: 0.2;
				color: var(--text);
				transition: var(--transition);

				&:hover{
					cursor: pointer;
					opacity: 1;
					color: var(--red);
					transition: var(--transition);
				}

				// Hide on mobile
				@media (max-width: @screenSM) {
					display: none;
				}
			}

			// Modal Title
			.modal-title{
				display: block;
				box-sizing: border-box;
				padding: 0 20px 0 20px;
				border-top-right-radius: var(--borderRadiusMd);
				border-top-left-radius: var(--borderRadiusMd);
				color: var(--text);
				font-weight: 600;
				letter-spacing: 0.25px;
				font-size: 20px;
				text-align: left;
				height: 66px;
				min-height: 66px;
				border-bottom: var(--borderWidth) solid var(--border);
				display: flex;
				justify-content: space-between;

				// Increase size and padding on mobile
				@media (max-width: @screenSM) {
					border-top-right-radius: 30px;
					border-top-left-radius: 30px;
					padding: 0 30px 0 30px;
				}

				span, i{
					display: flex;
					flex-direction: column;
					justify-content: center;
				}

				i{
					transform: scale(1.2);
				}

				// Image - floats right,
				img{
					height: 30px;
					margin-top: 16px;
					transition: var(--transition);
					transform: scale(1);

					@media (max-width: @screenSM) {
						margin-top: 18px;
					}

					&:hover{
						cursor: pointer;
						transform: scale(.95);
						transition: var(--transition);
					}

					// Invert for dark mode
					&.invert{
						filter: invert(100%);
					}
				}
			}

			// Main body/content
			.modal-body{
				box-sizing: border-box;
				padding: 20px;
				overflow: scroll;
				flex-grow: 3;

				@media (max-width: @screenSM) {
					padding: 20px 30px;
				}
			}

			// Modal footer/buttons
			.modal-footer{
				display: flex;
				justify-content: space-between;
				box-sizing: border-box;
				height: 66px;
				min-height: 70px;
				padding: 15px 20px;
				border-top: var(--borderWidth) solid var(--border);
				border-bottom-right-radius: var(--borderRadiusMd);
				border-bottom-left-radius: var(--borderRadiusMd);

				@media (max-width: @screenSM) {
					border-radius: 0;
					padding: 10px 5px;
					height: 66px;
					min-height: 66px;

					button{
						border-radius: 0;
						height: 100%;
						width: 100%;
						font-weight: 500;
						margin: 0 5px;
					}
				}

				// Center buttons
				&.center{
					// Space-around centers all elements with gaps between
					justify-content: space-around;
				}
				&.right{
					justify-content: flex-end;

					// Margin left on buttons for spacing
					button{
						margin-left: 12px;
					}
				}
				&.between{
					justify-content: space-between;
					padding-left: 0;
					padding-right: 0;
				}

				&.reverse{
					flex-direction: row-reverse;
				}
			}

			// Sizes
			// Small, confirmation modal
			&.small{
				max-width: 380px;

				// Absolute bottom, larger on mobile
				@media (max-width: @screenSM) {
					width: 100%;
					max-width: 100%;
				}

				// Main body/content
				.modal-body{
					font-size: 15px;
					letter-spacing: 0.6px;
					line-height: 24px;
					font-weight: 500;
					color: var(--textLight);
					padding: 30px 20px;

					@media (max-width: @screenSM) {
						padding: 44px 30px;
						font-size: 17px;
						line-height: 34px;
					}
				}
			}
		}
	}

</style>