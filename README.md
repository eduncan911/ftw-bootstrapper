ftwcoders-bootstrapper
======================

A convention-based approach using a MEF-like discovery pattern to register IoC objects using simple interfaces.

How does your StructureMap, Ninject, Unity, Castle or Autofac registration file(s) look?  One gigantic registration
file with 100s, or 1000s of lines of custom "Registrations" perhaps?  Maybe you switched to some of the newer 
"Self Registration" patterns now available, but still end up with complicated "Fluent API" registrations.

Here's the kicker: Ever considered switching to a different IoC?  "Dear God no!", right?  Because that would 
negate the years you've become an [insert-IoC-container-framework-of-choice] expert.  How about switching jobs
and finding out they do not use your IoC container of choice?

This, and more, is what I set out to solve years ago.  I came up with an idea: make the object itself
responsible for its own registration.  That way, it is tracked within that one object.

The Discoverability pattern
======================

After a few initial attempts, I got my hands on Preview 1 of the MEF.  Wow, a discoverability approach to registration!
This is exactly what I was looking for.

But I had a problem: I didn't want to lock myself into the MEF, especially when it was years from being released.

Also, the ye-old attribute-based programing model didn't flatter me.

Introducing: The IExportableAs<T> pattern
======================

This is it.  The meat the potatos of the whole thing:

    public class PostService : IPostService, IEntityService, 
                               IExportableAsShared<IPostService>, IExportableAsShared<IEntityService>
    { ... }

Notice the IExportableAs interfaces, which denote what "You want to export this object as."

That's it.  Move on with your code!

This even exposes a way to register factories.

    public class UserPostService : IUserPostService, 
                                   IExportableAsShared<IUserPostService, IUserPostFactory>
    { ... }
    
    public class UserPostFactory : IUserPostFactory, 
                                   IExportableFactory<IUserPostFactory, IUserPostService>
    {
      IUserPostService Create()
      { ... }
    }

IComposableFactory
======================

Even better, this entire framework is IoC agnostic: use whatever IoC container you want, and never worry about that
specific framework! 

This is done by exposing an IComposableFactory interface, which is implemented by whatever IoC container you want to use.

I am releasing this with the MEF and Castle Windsor versions of IComposableFactory.  Any other version is easy to 
create by implementing the IComposableFactory interface with simple Register<T>() and GetService<T>() methods.