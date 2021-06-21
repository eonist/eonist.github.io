My notes on AutoLayout for interface-builder<!--more-->

## Overview:
- ctrl drag between components in IB to setup constraints between components
- ctrl drag from component to parent container to create a constraint to the parent container

## Note:
Dont use IB or Xibs. Use programatic AutoLayout Because: 
- Because nothing holds more truth than pure code 🤓
- Xibs and storyboards are heavy, hard to maintain, hard to merge.
- They split the view concept into 2 separate files making debugging a nightmare
- There must be a better way
