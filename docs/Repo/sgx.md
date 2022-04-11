  
<iframe src="https://www.youtube.com/embed/2IEbQh8GEfU" height="480" width="640" allowFullScreen></iframe>
<hr />

Nathaniel  
SGX crate. And this is definitely one of our dependencies. The SGX crate is mostly a set of data structures that are related to SGX. So this is an Intel only crate. And for those of you who were able to attend my talk on how SGX works, you may have remembered me talking about the state save areas. And each time you enter, you have a different state save number. We have, for example, an implementation of the state save area here on line 77. So this is all the information that's stored in the state save area. And so you can actually read that information and write that information. And this is important, for example, when we're handling an exception, so like, if a syscall is implemented, we get an exception for that syscall. And it exits to the host. And then we reenter into the into the guest to handle that exception. And we do that by evaluating the contents of the state save area, which is this structure here. And then when we're done, we increment the the instruction pointer to skip the syscall instruction, and then resume. So all of this is pretty heavily used. There's a variety of data types here, all of which are related to SGX. And then we also have a two crypto implementations. The purpose for this is for signature data and for the digest, that's the other thing. So when you are loading, basically you create an enclave that's empty, and then you load a bunch of pages into it, and then you do the init instruction that fixes the Enclave contents. And whenever you update the pages, those pages are hashed, following a specific algorithm. And then you can use that hash as a measurement of the entire enclave. And so this provides us a way to calculate that hash outside of an enclave. So if we wanted to just, you know, run this process without support. We can even do this on like an Arm system, for example, where we can calculate what the hash of an enclave would be if it were running on SGX. The other thing is that when you're launching an SGX enclave, all enclaves must be signed. In an older version of SGX, they had to be signed by a key that was approved by Intel. And that's no longer the case. But the requirement for the signature has not disappeared. So what we actually do is, when we are launching an enclave, we do all the digest in parallel. So we're loading in a page, we not only load the page into the Enclave, we also run it through this digest function. And then right before we call it, we generate a random key, and then we sign the Enclave with that random key. And then we initialize, and because Intel doesn't care what key we signed with, that's fine. So yeah, that's what that crypto implementation is. This is just a crate with data structures, though, the actual logic for creating a key in an enclave is in the main repo. And the reason for that is that it's very hard to create a sort of generic API for enclave related stuff. It all ends up being mixed in with Enarx concerns. And so we don't split that out.