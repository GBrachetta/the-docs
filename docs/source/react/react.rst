React
=====

Basics
######

Functional Components
*********************

.. code-block:: jsx

  import React from 'react'

  const MyComponent = () => {
    return (
      <div></div>
    )
  }

  export default MyComponent

Stateful Components
*******************

.. code-block:: jsx

  import React, { Component } from 'react';

  class MyClassComponent extends Component {
    render() {
      return <div />;
    }
  }

  export default MyClassComponent;

Props
#####

State
#####

Implicit vs Explicit
####################

Consider the differences of impliciting invoquing Babel and using constructor

Implicit
********

.. code-block:: jsx

  class Lottery extends Component {
    state = { // State is set directly. Binding not necessary.
      nums : Array.from({ length: this.props.maxBalls }),
    };

    clickHandler = () => {
      this.setState((curState) => ({
        nums : curState.nums.map(
          () => Math.floor(Math.random() * this.props.maxNum) + 1,
        ),
      }));
    };

    render() {
      return (
        <section className="Lottery">
          <h1>{this.props.title}</h1>
          <div>{this.state.nums.map((n) => <Ball num={n} />)}</div>
          <button onClick={() => this.clickHandler()}>GENERATE</button> // Functions are executed
        </section>
      );
    }
  }

  export default Lottery;

Explicit
********

.. code-block:: jsx

  class Lottery extends Component {
    constructor(props) { // Use of constructor required
      super(props); // Must call super (and eventually pass props)
      this.state = { // Must be called with this
        nums : Array.from({ length: this.props.maxBalls }),
      };
      this.clickHandler = this.clickHandler.bind(this); // Functions must be bound
    }

    clickHandler() {
      this.setState((curState) => ({
        nums : curState.nums.map(
          () => Math.floor(Math.random() * this.props.maxNum) + 1,
        ),
      }));
    }

    render() {
      return (
        <section className="Lottery">
          <h1>{this.props.title}</h1>
          <div>{this.state.nums.map((n) => <Ball num={n} />)}</div>
          <button onClick={this.clickHandler}>GENERATE</button> // Functions are referenced
        </section>
      );
    }
  }

  export default Lottery;
