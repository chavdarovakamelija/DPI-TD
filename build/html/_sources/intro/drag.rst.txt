.. _intro-drag:

============================
Implementing Drag and Drop
============================

Setting Up React DnD
=============================
As the first step, we need to connect React DnD with our project. We are going to use the HTML5 Drag and Drop based back-end. There are specific back-ends for testing and touch.



The following three methods implement dragging of the board by holding down the mouse::

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
	
Go to new scroll position every time the mouse moves while dragging is activated::

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
	
	
Remove drag event listeners::

	handleMouseUp = () => {
		if (this.state.startX) {
		window.removeEventListener("mousemove", this.handleMouseMove);
		window.removeEventListener("mouseup", this.handleMouseUp);
		this.setState({ startX: null, startScrollX: null });
		}
	};


