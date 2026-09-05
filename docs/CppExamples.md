# C++ Examples

## Execute a friendly Twitch action

```cpp
UTwitchActionsAsync* Action = UTwitchActionsAsync::GetStreams(
    WorldContextObject, { TEXT("1234"), TEXT("5678") }, 20);
Action->OnSuccess.AddDynamic(Receiver, &UMyReceiver::HandleStreams);
Action->OnFailure.AddDynamic(Receiver, &UMyReceiver::HandleStreamingError);
Action->Activate();
```

For game code, prefer the authentication subsystem and async Blueprint/C++ action path so token lifetime, cancellation, and errors stay consistent. Direct clients are useful in native services and controlled tests.

In `HandleStreams`, call `UStreamingResponseLibrary::ParseStreams` to obtain provider-neutral `FStreamingStream` values and pagination. The friendly action encodes repeated Helix `user_id` keys correctly.

## Add a typed event component

```cpp
UTwitchEventsComponent* Events = Cast<UTwitchEventsComponent>(
    Actor->AddComponentByClass(UTwitchEventsComponent::StaticClass(),
                               false, FTransform::Identity, false));
Events->OnChatMessage.AddDynamic(Receiver, &UMyReceiver::HandleChat);
Events->RegisterChatCommand(TEXT("loadout"), true, 10.0f);
Events->Connect();
```

Use `UKickEventsComponent` for a Kick-only product, or `UStreamingEventsComponent` when Twitch and Kick run simultaneously.

## Select a provider at runtime

```cpp
UMultiStreamingSubsystem* Multi = GameInstance->GetSubsystem<UMultiStreamingSubsystem>();
Multi->SetSessionDefaultProvider(EStreamingProvider::Kick);

FStreamingApiRequest Request;
UMultiStreamingEndpointLibrary::MakeProviderRequest(
    Multi->ResolveProvider(EStreamingProviderSelection::ProjectDefault),
    TEXT("livestreams"), {}, {}, {}, Request);
```

## Verify a Kick webhook

```cpp
FStreamingRawEvent Event;
FStreamingError Error;
const bool bTrusted = UKickWebhookLibrary::VerifyAndParseWebhook(
    Headers, ExactRawRequestBody, UKickWebhookLibrary::GetKickPublicKeyPem(),
    600, Event, Error);
```

Never parse and reserialize the body before verification; signatures cover the exact bytes represented by the received body string.
