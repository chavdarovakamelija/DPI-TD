.. _intro-board:

========================
Board component
========================



To follow the principle of “lifting up state” we will keep the state of our Kanban board in the closest common ancestor (in this case <Board />) of all the child components that need access to the state. Any component can be stateful and keep track of its own local component state, but you can imagine how that could quickly get complicated. The preferred way of doing things is to use stateful components when necessary and leave components stateless when you can as they are easier to reason about. In keeping state in our <Board />component we are keeping a single source of truth in the closest common ancestor component.  Take a look at how we start setting up <Board /> ::

	import React, { Component } from "react";
	import PropTypes from "prop-types";
	import { connect } from "react-redux";
	import { Title } from "react-head";
	import { DragDropContext, Droppable } from "react-beautiful-dnd";
	import classnames from "classnames";
	import List from "../List/List";
	import ListAdder from "../ListAdder/ListAdder";
	import Header from "../Header/Header";
	import BoardHeader from "../BoardHeader/BoardHeader";
	import "./Board.scss";

	class Board extends Component {
	static propTypes = {
		lists: PropTypes.arrayOf(
		PropTypes.shape({ _id: PropTypes.string.isRequired })
		).isRequired,
		boardId: PropTypes.string.isRequired,
		boardTitle: PropTypes.string.isRequired,
		boardColor: PropTypes.string.isRequired,
		dispatch: PropTypes.func.isRequired
	};

	constructor(props) {
		super(props);
		this.state = {
		startX: null,
		startScrollX: null
		};
	}

	// boardId is stored in the redux store so that it is available inside middleware
	componentDidMount = () => {
		const { boardId, dispatch } = this.props;
		dispatch({
		type: "PUT_BOARD_ID_IN_REDUX",
		payload: { boardId }
		});
	};

	handleDragEnd = ({ source, destination, type }) => {
		// dropped outside the list
		if (!destination) {
		return;
		}
		const { dispatch, boardId } = this.props;

		// Move list
		if (type === "COLUMN") {
		// Prevent update if nothing has changed
		if (source.index !== destination.index) {
			dispatch({
			type: "MOVE_LIST",
			payload: {
				oldListIndex: source.index,
				newListIndex: destination.index,
				boardId: source.droppableId
			}
			});
		}
		return;
		}
		// Move card
		if (
		source.index !== destination.index ||
		source.droppableId !== destination.droppableId
		) {
		dispatch({
			type: "MOVE_CARD",
			payload: {
			sourceListId: source.droppableId,
			destListId: destination.droppableId,
			oldCardIndex: source.index,
			newCardIndex: destination.index,
			boardId
			}
		});
		}
	};
	
		// The following three methods implement dragging of the board by holding down the mouse
	handleMouseDown = ({ target, clientX }) => {
		if (target.className !== "list-wrapper" && target.className !== "lists") {
		return;
		}
		window.addEventListener("mousemove", this.handleMouseMove);
		window.addEventListener("mouseup", this.handleMouseUp);
		this.setState({
		startX: clientX,
      startScrollX: window.scrollX
		});
	};

	// Go to new scroll position every time the mouse moves while dragging is activated
	handleMouseMove = ({ clientX }) => {
		const { startX, startScrollX } = this.state;
		const scrollX = startScrollX - clientX + startX;
		window.scrollTo(scrollX, 0);
			const windowScrollX = window.scrollX;
			if (scrollX !== windowScrollX) {
				this.setState({
				startX: clientX + windowScrollX - startScrollX
			});
			}
		};

	// Remove drag event listeners
	handleMouseUp = () => {
		if (this.state.startX) {
		window.removeEventListener("mousemove", this.handleMouseMove);
		window.removeEventListener("mouseup", this.handleMouseUp);
		this.setState({ startX: null, startScrollX: null });
		}
	};

	handleWheel = ({ target, deltaY }) => {
		// Scroll page right or left as long as the mouse is not hovering a card-list (which could 		have vertical scroll)
		if (
		target.className !== "list-wrapper" &&
		target.className !== "lists" &&
		target.className !== "open-composer-button" &&
		target.className !== "list-title-button"
		) {
		return;
		}
		// Move the board 80 pixes on every wheel event
		if (Math.sign(deltaY) === 1) {
		window.scrollTo(window.scrollX + 80, 0);
		} else if (Math.sign(deltaY) === -1) {
		window.scrollTo(window.scrollX - 80, 0);
		}
	};

	
Now you actually have to render something. Looking at our state we have lists array with 4 objects in it, each one representing a list that you’ll see rendering out to the DOM. Here’s the common pattern::
	
	
	render = () => {
		const { lists, boardTitle, boardId, boardColor } = this.props;
		return (
		<>
			<div className={classnames("board", boardColor)}>
			<Title>{boardTitle} | React Kanban</Title>
			<Header />
			<BoardHeader />
				{/* eslint-disable jsx-a11y/no-static-element-interactions */}
			<div
				className="lists-wrapper"
				onMouseDown={this.handleMouseDown}
				onWheel={this.handleWheel}
			>
				{/* eslint-enable jsx-a11y/no-static-element-interactions */}
				<DragDropContext onDragEnd={this.handleDragEnd}>
				<Droppable
					droppableId={boardId}
					type="COLUMN"
					direction="horizontal"
				>
					{provided => (
					<div className="lists" ref={provided.innerRef}>
						{lists.map((list, index) => (
						<List
							list={list}
							boardId={boardId}
							index={index}
							key={list._id}
						/>
						))}
						{provided.placeholder}
						<ListAdder boardId={boardId} />
					</div>
					)}
				</Droppable>
				</DragDropContext>
			</div>
			<div className="board-underlay" />
			</div>
		</>
		);
	};
	}

	const mapStateToProps = (state, ownProps) => {
	const { board } = ownProps;
	return {
		lists: board.lists.map(listId => state.listsById[listId]),
		boardTitle: board.title,
		boardColor: board.color,
		boardId: board._id
	};
	};

	export default connect(mapStateToProps)(Board);

	
The render() method is required in all class components (stateful component s), it reads this.props and this.state and returns something. It’s pure in that it doesn’t modify component state or interact directly with the DOM, that’s supposed to be done in lifecycle methods like componentDidMount(). 