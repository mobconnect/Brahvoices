# Brahvoices
Package 
const asset = selectVoiceAsset('community-voice-1', {
  fpic: true,
  rightsHolderApproved: true,
  communityApproved: true,
  usageScope: 'app-playback'
});

export function selectVoiceAsset(id, context) {
  const asset = VOICE_REGISTRY.find(v => v.id === id);
  if (!asset) throw new Error('Unknown voice asset');
  if (!canUseVoice(asset, context)) throw new Error('Voice asset not approved for use');
  return asset;
}

export const VOICE_STATE = {
  STANDARD_TTS: 'standard-tts',
  PUBLIC_ACCESS_APPROVED: 'public-access-approved',
  COMMUNITY_APPROVED_RESTRICTED: 'community-approved-restricted'
};

export const VOICE_REGISTRY = [
  {
    id: 'au-default',
    label: 'Australian English default',
    state: VOICE_STATE.STANDARD_TTS,
    languageCode: 'en-AU',
    name: 'en-AU-Neural2-A'
  },
  {
    id: 'public-archive-1',
    label: 'Public-access approved archive audio',
    state: VOICE_STATE.PUBLIC_ACCESS_APPROVED,
    accessBasis: 'aiatsis-approved'
  },
  {
    id: 'community-voice-1',
    label: 'Community-approved restricted voice',
    state: VOICE_STATE.COMMUNITY_APPROVED_RESTRICTED,
    accessBasis: 'community-approved'
  }
];

export function canUseVoice(asset, context = {}) {
  if (asset.state === VOICE_STATE.STANDARD_TTS) return true;

  if (asset.state === VOICE_STATE.PUBLIC_ACCESS_APPROVED) {
    return asset.accessBasis === 'aiatsis-approved';
  }

  if (asset.state === VOICE_STATE.COMMUNITY_APPROVED_RESTRICTED) {
    return Boolean(
      context.rightsHolderApproved &&
      context.communityApproved &&
      context.fpic === true &&
      context.usageScope
    );
  }

  

  return false;
}

export const VOICE_REGISTRY = [
  {
    id: 'public-access-approved-1',
    label: 'Public-access approved audio',
    type: 'community-approved-audio',
    status: 'approved-for-use',
    accessBasis: 'aiatsis-approved'
  }
];

export const VOICE_REGISTRY = [
  {
    id: 'approved-elder-1',
    label: 'Approved Elder recording',
    type: 'community-approved-audio',
    languageCode: 'en-AU',
    voiceName: null,
    rightsHolderApproved: true,
    communityApproved: true,
    publicationApproved: true
  }
];

export function allowAsset(meta) {
  return Boolean(
    meta?.type === 'community-approved-audio' &&
    meta?.rightsHolderApproved === true &&
    meta?.communityApproved === true &&
    meta?.publicationApproved === true &&
    meta?.licenseUrl &&
    meta?.usageScope
  );
}

export function allowAsset(meta) {
  return Boolean(
    meta?.type === 'community-approved-audio' &&
    meta?.rightsHolderApproved === true &&
    meta?.communityApproved === true &&
    meta?.publicationApproved === true &&
    meta?.licenseUrl &&
    meta?.usageScope
  );
}


## Add to GitHub
GitHub says local code can be added with Git commands, then pushed to the remote repository. Use:[3][4]

```bash
git init
git add .
git commit -m "Add consent-controlled community TTS package"
git branch -M main
git remote add origin https://github.com/jessieleighbright.com/mobconnect.git
git push -u origin main

npm run example

# MobConnect Community TTS

Consent-controlled speech package for app builders.

## What it does
- Standard Australian English TTS via Google Cloud.
- Community-approved Indigenous audio assets only.
- Blocks any asset that lacks FPIC, rights-holder approval, or privacy review.

## Install
```bash
npm install

import { synthesizeToFile, VOICE_REGISTRY } from '../src/index.js';

console.log(VOICE_REGISTRY.map(v => v.label));

const file = await synthesizeToFile({
  text: 'Welcome to the app.',
  voiceId: 'au-default',
  outputFile: 'output/welcome.mp3'
});

console.log(file);

export { approveVoiceAsset } from './approveVoiceAsset.js';
export { VOICE_REGISTRY } from './voiceRegistry.js';
export { synthesizeToFile } from './synthesize.js';

import textToSpeech from '@google-cloud/text-to-speech';
import fs from 'fs';
import path from 'path';
import { VOICE_REGISTRY } from './voiceRegistry.js';
import { approveVoiceAsset } from './approveVoiceAsset.js';

const client = new textToSpeech.TextToSpeechClient();

function getVoice(id = 'au-default', meta = {}) {
  const voice = VOICE_REGISTRY.find(v => v.id === id) || VOICE_REGISTRY[0];
  if (voice.type === 'community-approved-audio') {
    if (!approveVoiceAsset(meta)) throw new Error('Indigenous asset blocked: approval and consent required.');
    return { languageCode: voice.languageCode, name: meta.voiceName || null };
  }
  return { languageCode: voice.languageCode, name: voice.name };
}

export async function synthesizeToFile({ text, voiceId = 'au-default', outputFile = 'output/speech.mp3', meta = {}, speakingRate = 1.0, pitch = 0.0 }) {
  const voice = getVoice(voiceId, meta);
  const request = {
    input: { text },
    voice,
    audioConfig: { audioEncoding: 'MP3', speakingRate, pitch }
  };
  const [response] = await client.synthesizeSpeech(request);
  fs.mkdirSync(path.dirname(outputFile), { recursive: true });
  fs.writeFileSync(outputFile, response.audioContent, 'binary');
  return outputFile;
}

export const VOICE_REGISTRY = [
  { id: 'au-default', type: 'tts', languageCode: 'en-AU', name: 'en-AU-Neural2-A', label: 'Australian English - default' },
  { id: 'au-narration', type: 'tts', languageCode: 'en-AU', name: 'en-AU-Wavenet-C', label: 'Australian English - narration' },
  { id: 'approved-indigenous-1', type: 'community-approved-audio', languageCode: 'en-AU', name: null, label: 'Community-approved Indigenous voice asset' }
];
export function approveVoiceAsset(meta) {
  return Boolean(
    meta?.type === 'community-approved-audio' &&
    meta?.fpic === true &&
    meta?.rightsHolderApproved === true &&
    meta?.communityApproved === true &&
    meta?.licenseUrl &&
    meta?.usageScope &&
    meta?.privacyReviewPassed === true
  );
}
{
  "name": "mobconnect-community-tts",
  "version": "1.0.0",
  "type": "module",
  "main": "src/index.js",
  "license": "MIT",
  "scripts": {
    "example": "node examples/node-example.js"
  },
  "dependencies": {
    "@google-cloud/text-to-speech": "^5.6.0"
  }
}

// src/approveVoiceAsset.js
export function approveVoiceAsset(meta) {
  return Boolean(
    meta?.type === 'community-approved-audio' &&
    meta?.fpic === true &&
    meta?.rightsHolderApproved === true &&
    meta?.communityApproved === true &&
    meta?.licenseUrl &&
    meta?.usageScope &&
    meta?.privacyReviewPassed === true
  );
}

// src/voiceRegistry.js
export const VOICE_REGISTRY = [
  { id: 'au-default', type: 'tts', languageCode: 'en-AU', name: 'en-AU-Neural2-A', label: 'Australian English - default' },
  { id: 'au-narration', type: 'tts', languageCode: 'en-AU', name: 'en-AU-Wavenet-C', label: 'Australian English - narration' },
  { id: 'approved-indigenous-1', type: 'community-approved-audio', languageCode: 'en-AU', name: null, label: 'Community-approved Indigenous voice asset' }
];

// src/index.js
export { approveVoiceAsset } from './approveVoiceAsset.js';
export { VOICE_REGISTRY } from './voiceRegistry.js';
export { synthesizeToFile } from './synthesize.js';


