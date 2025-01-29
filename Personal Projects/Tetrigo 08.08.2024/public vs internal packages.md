# Public vs Internal Packages
Determining what functionality should live in the public package (`tetrigo/pkg/tetris`) and what should live in the internal packages (eg. `tetrigo/internal/*`). Note, these internal packages should be separated from the UI implementations (`tetrigo/cmd/*`).

```
tetrigo/
├── cmd/
│   └── tui/
│       └── ...
├── internal/ 
│   └── ...
└── pkg/ 
    └── tetris/ 
        └── marathon/ 
            └── ...
```

`cmd/tui/`
- UI code for a TUI format.
`internal/`
- Helpers not specifically relating to Tetris, but which can be shared between UI formats (eg. config).
`pkg/tetris/`
- Generic helpers relating to Tetris but not specific game modes.
`pkg/tetris/marathon/`
- Helpers relating to the standard marathon game mode.



