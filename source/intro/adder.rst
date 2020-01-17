.. _intro-adder:

========================
Card adder component
========================

CardAdder component::

	import React, { Component } from "react";
	import PropTypes from "prop-types";
	import { connect } from "react-redux";
	import Textarea from "react-textarea-autosize";
	import shortid from "shortid";
	import ClickOutside from "../ClickOutside/ClickOutside";
	import "./CardAdder.scss";

	class CardAdder extends Component {
	static propTypes = {
		listId: PropTypes.string.isRequired,
		dispatch: PropTypes.func.isRequired
	};

	constructor() {
		super();
		this.state = {
		newText: "",
		isOpen: false
		};
	}

	toggleCardComposer = () => {
		this.setState({ isOpen: !this.state.isOpen });
	};

	handleChange = event => {
		this.setState({ newText: event.target.value });
	};

	handleKeyDown = event => {
		if (event.keyCode === 13 && event.shiftKey === false) {
		this.handleSubmit(event);
		} else if (event.keyCode === 27) {
		this.toggleCardComposer();
		}
	};

	handleSubmit = event => {
		event.preventDefault();
		const { newText } = this.state;
		const { listId, dispatch } = this.props;
		if (newText === "") return;

		const cardId = shortid.generate();
		dispatch({
		type: "ADD_CARD",
		payload: { cardText: newText, cardId, listId }
		});
		this.toggleCardComposer();
		this.setState({ newText: "" });
	};

	render() {
		const { newText, isOpen } = this.state;
		return isOpen ? (
		<ClickOutside handleClickOutside={this.toggleCardComposer}>
			<form
			onSubmit={this.handleSubmit}
			className="card-adder-textarea-wrapper"
			>
			<Textarea
				autoFocus
				useCacheForDOMMeasurements
				minRows={1}
				onChange={this.handleChange}
				onKeyDown={this.handleKeyDown}
				value={newText}
				className="card-adder-textarea"
				placeholder="Add a new card..."
				spellCheck={false}
				onBlur={this.toggleCardComposer}
			/>
			</form>
		</ClickOutside>
		) : (
		<button onClick={this.toggleCardComposer} className="add-card-button">
			+
		</button>
		);
	}
	}

	export default connect()(CardAdder);

CardAdder.scss::

	@import "../../variables.scss";

	.card-adder-textarea-wrapper {
	margin: 6px 13px 0 13px;
	}

	.card-adder-textarea {
	float: right; // removes mysterious empty space at the bottom of the textarea somehow
	box-sizing: border-box;
	width: 100%;
	padding: 10px 8px;
	border: 0;
	border-radius: 3px;
	color: inherit;
	font-family: inherit;
	font-size: 16px;
	resize: none;
	}

	.add-card-button {
	align-self: center;
	flex-shrink: 0;
	width: 39px;
	height: 39px;
	margin-top: 6px;
	border: none;
	border-radius: $list-radius;
	color: $white;
	background: $transparent-black;
	font-size: 28px;
	transition: background 0.1s;
	cursor: pointer;
	}

	.add-card-button:hover,
	.add-card-button:focus {
	background: $less-transparent-black;
	}
