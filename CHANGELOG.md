## [_Unreleased_](https://github.com/freckle/freckle-prelude/compare/v0.1.0.0...main)

## [v0.1.0.0](https://github.com/freckle/freckle-prelude/compare/v0.0.4.1...v0.1.0.0)

- Remove `throwString :: (MonadIO m, HasCallStack) => String -> m a`
- Remove `fromJustNoteM :: (MonadIO m, HasCallStack) => String -> Maybe a -> m a`
- Remove `error :: HasCallStack => String -> a`
- Remove `errorWithoutStackTrace :: String -> a`
- Remove `fail :: MonadFail m => String -> m a` (the `MonadFail` class is still exported, without its method)
- Add `throw :: Exception e => e -> a`, re-exported from `Control.Exception` in `base`

Throwing exceptions from strings is discouraged. Instead, define a dedicated
exception type and throw it with
`throwM :: (Exception e, MonadIO m, HasCallStack) => e -> m a` in a monadic
context, or with `throw` in pure code. Some uses of `fail` are legitimate, such
as writing an aeson `Parser`. For those, import `fail` explicitly from
`Control.Monad.Fail`.

Defining a dedicated exception type takes a few lines. Where you previously
threw a constant string, a type with no fields is enough:

```haskell
-- Before
throwString "Default grading scale is missing"

-- After
throwM DefaultGradingScaleMissing

data DefaultGradingScaleMissing = DefaultGradingScaleMissing
  deriving stock (Show)
  deriving anyclass (Exception)
```

Where the string was built by concatenating values, those values become fields
of the exception instead:

```haskell
-- Before
throwString $ "User " <> show userId <> " is not in classroom " <> show classroomId

-- After
throwM UserNotInClassroom {userId, classroomId}

data UserNotInClassroom = UserNotInClassroom
  { userId :: UserId
  , classroomId :: ClassroomId
  }
  deriving stock (Show)
  deriving anyclass (Exception)
```

If you want to keep a human-readable message rather than the derived `Show`
output, remove `deriving anyclass (Exception)` and define the instance
yourself, with `displayException`:

```haskell
instance Exception UserNotInClassroom where
  displayException e =
    "User " <> show e.userId <> " is not in classroom " <> show e.classroomId
```

## [v0.0.4.1](https://github.com/freckle/freckle-prelude/tree/v0.0.4.1)

Moved version control to https://github.com/freckle/freckle-prelude

## [v0.0.4.0](https://github.com/freckle/freckle-app/compare/freckle-prelude-v0.0.3.0...freckle-prelude-v0.0.4.0)

Add `Natural` from `Numeric.Natural` in `base`.

## [v0.0.3.0](https://github.com/freckle/freckle-app/compare/freckle-prelude-v0.0.2.0...freckle-prelude-v0.0.3.0)

Add `Type` and `Constraint`, from the `Data.Kind` module in `base`.

## [v0.0.2.0](https://github.com/freckle/freckle-app/compare/freckle-prelude-v0.0.1.1...freckle-prelude-v0.0.2.0)

Add `Void`

## [v0.0.1.1](https://github.com/freckle/freckle-app/tree/freckle-prelude-v0.0.0.0/freckle-prelude)

First release, sprouted from `freckle-app-1.20.0.1`.
