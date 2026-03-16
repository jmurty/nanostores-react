# Nano Stores React

<img align="right" width="92" height="92" title="Nano Stores logo"
     src="https://nanostores.github.io/nanostores/logo.svg">

React integration for **[Nano Stores]**, a tiny state manager
with many atomic tree-shakable stores.

- **Small.** Less than 1 KB. Zero dependencies.
- **Fast.** With small atomic and derived stores, you do not need to call
  the selector function for all components on every store change.
- **Tree Shakable.** The chunk contains only stores used by components
  in the chunk.
- Was designed to move logic from components to stores.
- It has good **TypeScript** support.

```tsx
import { useStore } from '@nanostores/react'
import { $profile } from '../stores/profile.js'

export const Header = ({ postId }) => {
  const profile = useStore($profile)
  return <header>Hi, {profile.name}</header>
}
```

<h2>Options</h2>

<h3>Keys</h3>

Use the `keys` option to re-render only on specific key changes:

```tsx
export const Header = () => {
  const profile = useStore($profile, { keys: 'name' })
  return <header>{profile.name}</header>
}
```

<h3>SSR</h3>

Use the `ssr` option if you need to fix hydration errors when loading
server-side rendered (SSR) pages.

Hydration errors can happen when the client-side store in the browser gets out
of sync with the store the server used to render the HTML being hydrated. For
example when using Astro with a global nanostore updated on the server- and the
client-side, server rendered pages, and `<ClientRouter />` for client-side
routing.

For simple cases where you only update the store on the client-side, not on the
server, set `ssr:true`. This tells `useStore` to always return the store's
initial value on the server, and to use this initial value for hydration on the
client:

```tsx
export const Header = () => {
  const profile = useStore($profile, { ssr: true })

  // Server render and client hydration use store's initial value.
  // After hydration, client re-renders with the current value.
  return <header>{profile.name}</header>
}
```

For advanced cases where you update store values on the server, set a function
that returns the store's value when the HTML is rendered so the client can use
this same value to hydrate: `ssr: () => serverState`.

```tsx
// Value of store on server at time of SSR, passed to client somehow...
const profileFromServer = { name: 'A User' }

export const Header = () => {
  const profile = useStore($profile, {
    // On server, always use up-to-date store value (`ssr` is undefined).
    // On client, set server value to avoid error on hydration.
    ssr: typeof window === 'undefined' ? undefined : () => profileFromServer
  })

  // Server render uses store's current value. Client uses value from function
  // for hydration, and after hydration re-renders with the current value.
  return <header>{profile.name}</header>
}
```

The function set on `ssr` is provided to React's `useSyncExternalStore` as the
`getServerSnapshot` option.

[Nano Stores]: https://github.com/nanostores/nanostores/

---

<img src="https://cdn.evilmartians.com/badges/logo-no-label.svg" alt="" width="22" height="16" /> Made at <b><a href="https://evilmartians.com/devtools?utm_source=nanostores-react&utm_campaign=devtools-button&utm_medium=github">Evil Martians</a></b>, product consulting for <b>developer tools</b>.

---
